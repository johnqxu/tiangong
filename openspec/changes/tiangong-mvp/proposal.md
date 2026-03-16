# 天工 - 研发资源管理平台 MVP

## Why

当前研发资源管理完全依赖线下流程，1000人/10个部门/20个小组的资源调度靠人工治理，流程混乱、效率低下。缺乏统一的：
- 资源饱和度可视化
- 技能与任务匹配机制
- 未来资源缺口预测

需要一个线上化平台，让资源管理从"Excel+人工协调"升级为"数据驱动+AI辅助决策"。

## What Changes

### 新增能力

- **后端 API 服务** - FastAPI + PostgreSQL，提供完整的资源管理 RESTful API
- **组织架构管理** - 部门、小组、人员的层级管理
- **技能体系管理** - 支持技术栈和业务知识两大类，支持层级技能树
- **项目与 WBS 管理** - 项目管理 + 任务树形分解，支持任务需求定义（角色、技能、工时、时间窗口）
- **资源匹配引擎** - 基于角色+技能+时间的自动匹配算法，推荐最优候选人
- **资源透视洞察** - 多维度饱和度分析（个人/小组/部门/技能）、时间轴视图、缺口预警
- **数据导入** - Excel 模板 + 批量导入，支持组织、人员、技能、任务的快速初始化
- **OpenCode Skills** - 为 AI 开发工具（OpenCode）提供交互式资源管理能力

### 移除/简化

- **无 Web 前端** - MVP 阶段不开发管理界面，所有交互通过 AI 工具 + API 完成
- **无认证授权** - MVP 阶段 API 暂不实现认证，后续迭代补充

## Capabilities

### New Capabilities

- `organization-management`: 组织架构管理（部门、小组、人员的 CRUD 及层级关系）
- `skill-management`: 技能体系管理（技能分类、技能定义、人员技能关联）
- `project-management`: 项目管理（项目 CRUD、状态管理）
- `task-wbs`: 任务与 WBS 管理（树形任务结构、任务需求定义、状态流转）
- `resource-matching`: 资源匹配（匹配算法、候选人推荐、分配管理）
- `resource-insights`: 资源透视洞察（饱和度分析、时间轴视图、技能供需、缺口预测）
- `data-import`: 数据导入（Excel 模板、批量导入、导入结果反馈）
- `opencode-skills`: OpenCode Skills（8个核心 Skill，覆盖组织、技能、项目、WBS、匹配、洞察）

### Modified Capabilities

(无 - 这是全新项目)

## Impact

### 代码结构

```
tiangong/
├── backend/           # FastAPI 后端服务
│   ├── app/
│   │   ├── api/       # API 路由
│   │   ├── models/    # SQLAlchemy 模型
│   │   ├── schemas/   # Pydantic 模型
│   │   ├── services/  # 业务逻辑
│   │   └── utils/     # 工具函数
│   ├── migrations/    # Alembic 迁移
│   └── tests/         # 测试
├── skills/            # OpenCode Skills
│   └── opencode/      # OpenCode 格式
└── templates/         # Excel 导入模板
```

### 技术栈

- 后端: Python 3.11+ / FastAPI / SQLAlchemy 2.0
- 数据库: PostgreSQL 16
- 缓存: Redis 7
- 部署: Docker Compose
- AI 工具集成: OpenCode Skills

### 性能要求

- 写入: 20 TPS（任务分解、分配）
- 读取: 200 TPS（透视查询）
- 单体架构足够支撑，无需微服务

### 用户角色

- 超级管理员: 全部权限
- 部门经理: 管理本部门
- 组长: 管理本组
- PM: 管理项目、资源申请
- 普通成员: 查看自己的任务
