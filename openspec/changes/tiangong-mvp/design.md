# 天工 - 技术设计文档

## Context

### 背景

当前研发组织（1000人/10部门/20小组）的资源管理完全依赖线下流程：
- Excel 表格记录人员信息
- 钉钉/飞书群线下沟通任务分配
- PM 手动协调资源冲突
- 无统一的饱和度可视化
- 无技能与任务的系统化匹配

### 约束

- **无前端界面** - MVP 阶段所有交互通过 AI 工具 + API
- **无认证** - API 暂不实现认证
- **性能要求** - 20 TPS 写入，200 TPS 读取
- **部署方式** - Docker Compose 单机部署
- **AI 工具优先** - OpenCode Skills 是主要交互方式

### 目标用户

- PM: 项目创建、WBS分解、资源申请
- 组长: 查看组内负载、确认任务分配
- 管理层: 资源洞察、缺口预测
- 普通成员: 查看个人任务

## Goals / Non-Goals

### Goals

1. 提供完整的资源管理 RESTful API
2. 实现组织、技能、项目、任务的 CRUD
3. 实现基于角色+技能+时间的资源匹配算法
4. 实现多维度资源透视（饱和度、时间轴、技能供需）
5. 提供 Excel 模板和批量导入能力
6. 提供 8 个核心 OpenCode Skills

### Non-Goals

1. Web 管理界面（后续迭代）
2. API 认证授权（后续迭代）
3. 工时填报与审批（后续迭代）
4. 外部系统集成（Jira、飞书等）
5. 支持 Claude Code / Cursor（后续迭代）
6. 移动端支持
7. **多租户/多组织隔离** - MVP 单组织部署，以最小成本实现核心功能
8. **历史项目数据导入** - 不导入历史数据，业务熟悉度仅基于当前系统内的业务技能
9. **时区支持** - 国内团队使用，统一使用北京时间 (UTC+8)，无需时区转换

## Decisions

### D1: 技术栈选择

**决定**: Python + FastAPI + SQLAlchemy + PostgreSQL + Redis

**理由**:
- FastAPI 性能优秀（基于 Starlette），原生支持异步和类型提示
- SQLAlchemy 2.0 支持 async + typing，复杂查询能力强
- PostgreSQL 支持 JSONB（半结构化数据）、递归查询（树形结构）、窗口函数（透视计算）
- Redis 用于缓存热点查询（饱和度计算）

**备选方案**:
- Go + Gin: 性能更好，但 Python 生态更适合 AI 工具集成
- Node.js + NestJS: 类型安全，但团队 Python 熟悉度更高

### D2: 架构模式

**决定**: 模块化单体

**理由**:
- 1000 人规模 + 200 TPS，单体完全够用
- 开发速度快，部署简单
- 模块化设计便于未来拆分

**模块划分**:
```
app/
├── api/v1/           # API 路由层
│   ├── org.py        # 组织管理
│   ├── skills.py     # 技能管理
│   ├── projects.py   # 项目管理
│   ├── tasks.py      # 任务管理
│   ├── matching.py   # 资源匹配
│   ├── insights.py   # 透视洞察
│   └── import.py     # 数据导入
├── models/           # 数据模型
├── schemas/          # Pydantic 模型
├── services/         # 业务逻辑
│   ├── matching.py   # 匹配算法
│   └── insights.py   # 透视计算
└── utils/
```

### D3: 数据模型设计

**核心实体关系**:
```
Organization (1) ──┬── (n) Department ──── (n) Group ──── (n) Member
                   │                            │
                   │                            └── (n) MemberSkill ──── (1) Skill
                   │                                    │
                   └────────────────────────────────────┘
                           
Project (1) ──── (n) Task (树形自关联)
     │
     └── Task ├── required_role
              ├── required_skills (JSONB)
              ├── estimated_hours
              └── time_window (JSONB)

Task (1) ──── (n) Assignment ──── (1) Member
                    │
                    ├── match_score
                    ├── match_details (JSONB)
                    └── time_allocation (JSONB)
```

**关键字段设计**:

| 表 | 字段 | 类型 | 说明 |
|---|---|---|---|
| tasks | required_skills | JSONB | `[{"skill_id": "...", "level": 3, "weight": 1.0}]` |
| tasks | time_window | JSONB | `{"start": "2024-04-01", "end": "2024-05-31"}` |
| assignments | time_allocation | JSONB | `{"2024-04": 40, "2024-05": 40}` |
| assignments | match_details | JSONB | `{"role_score": 100, "skill_score": 85, ...}` |
| skills | level_definition | JSONB | `{"1": {"name": "入门"}, ...}` |

### D4: 资源匹配算法

**决定**: 加权评分模型

**公式**:
```
Score = w1 × 角色匹配 + w2 × 技能匹配 + w3 × 时间可用 + w4 × 业务熟悉度

默认权重:
- w1 (角色) = 0.30
- w2 (技能) = 0.35
- w3 (时间) = 0.25
- w4 (业务) = 0.10
```

**各维度评分**:

| 维度 | 评分逻辑 | 分值范围 |
|---|---|---|
| 角色 | 完全匹配=100, 部分匹配=50, 不匹配=0 | 0-100 |
| 技能 | 按技能逐一匹配，加权平均 | 0-100 |
| 时间 | `可用工时 / 需求工时 × 100` | 0-100+ |
| 业务 | 历史项目经验 + 业务技能 | 0-100 |

**技能匹配细节**:
```python
def skill_match(required: SkillReq, member: MemberSkill) -> float:
    if member.skill_id == required.skill_id:
        if member.level >= required.level:
            return 100
        else:
            return member.level / required.level * 80
    # 父技能降级匹配
    if is_parent_skill(member.skill_id, required.skill_id):
        return 50
    return 0
```

### D5: 透视计算策略

**决定**: 预计算 + 缓存

**饱和度计算**:
```
饱和度 = 已分配工时 / 可用工时 × 100%

按月计算:
- 可用工时 = 成员周工时 × 4.3
- 已分配工时 = Σ(该月所有任务的 time_allocation)
```

**缓存策略**:
- 透视结果缓存 5 分钟
- 任务/分配变更时清除相关缓存
- 使用 Redis 存储

**SQL 示例（按月饱和度）**:
```sql
SELECT 
    m.id,
    m.name,
    m.weekly_hours * 4.3 as available_hours,
    COALESCE(SUM(
        (a.time_allocation->>'2024-04')::float
    ), 0) as assigned_hours
FROM members m
LEFT JOIN assignments a ON a.member_id = m.id
LEFT JOIN tasks t ON t.id = a.task_id
    AND t.time_window->>'start' <= '2024-04-30'
    AND t.time_window->>'end' >= '2024-04-01'
WHERE m.group_id = :group_id
GROUP BY m.id
```

### D6: Excel 导入设计

**决定**: openpyxl + 批量 API

**模板文件**:
| 模板 | Sheet | 数据 |
|---|---|---|
| org-import.xlsx | Departments | 部门编码、名称、上级、负责人 |
| | Groups | 小组编码、名称、部门、组长 |
| member-import.xlsx | Members | 工号、姓名、邮箱、角色、小组、周工时 |
| skill-import.xlsx | Skills | 分类、编码、名称、父技能 |
| | MemberSkills | 工号、技能编码、等级、年限 |
| task-import.xlsx | Tasks | 项目、任务名、父任务、角色、技能、工时、时间 |

**导入流程**:
1. 下载模板
2. 用户填写
3. 上传文件
4. 后端校验 → 返回校验结果
5. 用户确认 → 批量创建
6. 返回导入报告

### D7: OpenCode Skills 设计

**决定**: 8 个核心 Skill

| Skill | 触发词 | 功能 |
|---|---|---|
| tiangong-org | 组织、部门、小组 | 组织管理 CRUD |
| tiangong-skill | 技能、定义 | 技能管理 CRUD |
| tiangong-project | 项目 | 项目管理 CRUD |
| tiangong-wbs | 任务、WBS、分解 | 任务管理 + AI 辅助分解 |
| tiangong-match | 推荐、匹配、分配 | 资源匹配与分配 |
| tiangong-insight | 饱和度、负载、缺口 | 透视洞察 |
| tiangong-import | 导入、Excel | 数据导入 |
| tiangong-report | 报告、周报 | 报告生成 |

**Skill 结构**:
```
skills/opencode/
├── tiangong-org/
│   └── SKILL.md
├── tiangong-skill/
│   └── SKILL.md
├── tiangong-project/
│   └── SKILL.md
├── tiangong-wbs/
│   └── SKILL.md
├── tiangong-match/
│   └── SKILL.md
├── tiangong-insight/
│   └── SKILL.md
├── tiangong-import/
│   └── SKILL.md
└── tiangong-report/
    └── SKILL.md
```

## Risks / Trade-offs

### R1: 无认证的安全风险

**风险**: API 无认证，任何人都可访问

**缓解**:
- 部署在内网环境
- 文档明确标注 MVP 阶段限制
- 后续迭代优先添加认证

### R2: Excel 导入数据质量

**风险**: 用户填写的 Excel 数据可能格式错误、数据冲突

**缓解**:
- 详细的校验规则
- 校验报告清晰标注错误行
- 提供"仅校验不导入"模式
- 导入前预览

### R3: 匹配算法准确性

**风险**: 自动匹配可能推荐不合适的人选

**缓解**:
- 匹配结果只是建议，需人工确认
- 显示详细的匹配分析，让用户理解推荐原因
- 权重可配置
- 后续可基于反馈优化算法

### R4: 技能体系维护成本

**风险**: 技能定义可能过时或不准确

**缓解**:
- 技能体系支持动态调整
- 人员技能支持自评+主管确认（后续迭代）
- 提供"技能过期"提醒机制（后续迭代）

### T1: 无前端的用户体验

**权衡**: MVP 不开发前端，依赖 AI 工具交互

**取舍**:
- 优点: 开发速度快，聚焦核心能力
- 缺点: 非技术用户门槛高
- 决定: MVP 面向 PM/组长/管理层，都是技术背景，可接受

## Migration Plan

### 部署步骤

1. **准备环境**
   ```bash
   # 克隆代码
   git clone <repo>
   cd tiangong
   
   # 配置环境变量
   cp .env.example .env
   # 编辑 .env 配置数据库连接
   ```

2. **启动服务**
   ```bash
   docker-compose up -d
   
   # 运行数据库迁移
   docker-compose exec api alembic upgrade head
   ```

3. **导入初始数据**
   - 下载 Excel 模板
   - 填写组织、人员、技能数据
   - 通过 API 或 Skill 导入

4. **配置 AI 工具**
   - 复制 skills/ 目录到 OpenCode 配置目录
   - 配置 API 地址

### 回滚策略

```bash
# 停止服务
docker-compose down

# 回滚数据库
docker-compose exec api alembic downgrade -1

# 恢复数据备份
pg_restore -d tiangong backup.dump
```

## Decisions Resolved

以下问题已在探索阶段明确：

| 问题 | 决定 | 理由 |
|------|------|------|
| 技能等级定义标准化 | 不预置模板，由用户自定义 | 不同团队技能体系差异大，预置模板反而限制灵活性 |
| 多租户/多组织支持 | **不支持** | MVP 单组织部署，以最小成本实现核心功能 |
| 历史项目数据导入 | **不导入** | 业务熟悉度仅基于当前系统内的人员业务技能，简化实现 |
| 时区处理 | **不考虑** | 国内团队使用，统一北京时间 (UTC+8)，无需时区转换 |
