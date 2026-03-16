# OpenCode Skills

## ADDED Requirements

### Requirement: 组织管理 Skill

系统 SHALL 提供 tiangong-org Skill 用于组织管理。

#### Scenario: 查询组织架构
- **WHEN** 用户通过 Skill 查询组织架构
- **THEN** Skill 调用 API 返回组织架构信息，并以可读格式展示

#### Scenario: 创建部门或小组
- **WHEN** 用户通过 Skill 创建部门或小组
- **THEN** Skill 调用 API 创建实体并返回结果

---

### Requirement: 技能管理 Skill

系统 SHALL 提供 tiangong-skill Skill 用于技能管理。

#### Scenario: 查询技能树
- **WHEN** 用户通过 Skill 查询技能树
- **THEN** Skill 调用 API 并以树形格式展示技能层级

#### Scenario: 查询拥有某技能的人员
- **WHEN** 用户通过 Skill 查询某技能的人员
- **THEN** Skill 返回人员列表及技能等级

#### Scenario: 为人员添加技能
- **WHEN** 用户通过 Skill 为人员添加技能
- **THEN** Skill 调用 API 更新人员技能

---

### Requirement: 项目管理 Skill

系统 SHALL 提供 tiangong-project Skill 用于项目管理。

#### Scenario: 创建项目
- **WHEN** 用户通过 Skill 描述项目信息
- **THEN** Skill 解析描述并调用 API 创建项目

#### Scenario: 查询项目
- **WHEN** 用户通过 Skill 查询项目
- **THEN** Skill 调用 API 返回项目列表

#### Scenario: 更新项目状态
- **WHEN** 用户通过 Skill 更新项目状态
- **THEN** Skill 调用 API 更新状态

---

### Requirement: WBS 管理 Skill

系统 SHALL 提供 tiangong-wbs Skill 用于任务和 WBS 管理。

#### Scenario: 创建任务
- **WHEN** 用户通过 Skill 描述任务
- **THEN** Skill 解析描述并调用 API 创建任务

#### Scenario: 查看项目 WBS
- **WHEN** 用户通过 Skill 查看项目 WBS
- **THEN** Skill 以 ASCII 树形格式展示任务结构

#### Scenario: AI 辅助任务分解
- **WHEN** 用户请求 AI 帮助分解任务
- **THEN** Skill 分析任务描述，生成分解建议供用户确认

#### Scenario: 更新任务状态
- **WHEN** 用户通过 Skill 更新任务状态
- **THEN** Skill 调用 API 更新状态

---

### Requirement: 资源匹配 Skill

系统 SHALL 提供 tiangong-match Skill 用于资源匹配。

#### Scenario: 推荐候选人
- **WHEN** 用户通过 Skill 为任务请求推荐
- **THEN** Skill 调用匹配 API 并展示候选人列表及匹配分析

#### Scenario: 分配任务
- **WHEN** 用户通过 Skill 分配任务
- **THEN** Skill 调用 API 创建分配并返回结果

#### Scenario: 确认分配
- **WHEN** 被分配人通过 Skill 确认任务
- **THEN** Skill 调用 API 更新分配状态

---

### Requirement: 资源洞察 Skill

系统 SHALL 提供 tiangong-insight Skill 用于资源透视。

#### Scenario: 查看饱和度
- **WHEN** 用户通过 Skill 查询饱和度
- **THEN** Skill 调用 API 并以可视化格式展示饱和度

#### Scenario: 查看时间轴
- **WHEN** 用户通过 Skill 查看时间轴
- **THEN** Skill 以 ASCII 图表展示时间轴

#### Scenario: 技能缺口分析
- **WHEN** 用户通过 Skill 分析技能缺口
- **THEN** Skill 调用 API 并展示供需对比和建议

#### Scenario: 异常检测
- **WHEN** 用户通过 Skill 检查异常
- **THEN** Skill 返回异常列表和处理建议

---

### Requirement: 数据导入 Skill

系统 SHALL 提供 tiangong-import Skill 用于数据导入。

#### Scenario: 下载模板
- **WHEN** 用户通过 Skill 请求导入模板
- **THEN** Skill 提供 Excel 模板下载链接

#### Scenario: 执行导入
- **WHEN** 用户提供 Excel 文件
- **THEN** Skill 调用导入 API 并返回导入结果

---

### Requirement: 报告生成 Skill

系统 SHALL 提供 tiangong-report Skill 用于生成报告。

#### Scenario: 生成周报
- **WHEN** 用户通过 Skill 请求周报
- **THEN** Skill 调用 API 并生成 Markdown 格式周报

#### Scenario: 生成月报
- **WHEN** 用户通过 Skill 请求月报
- **THEN** Skill 调用 API 并生成 Markdown 格式月报

---

### Requirement: Skill 文件结构

系统 SHALL 按标准格式组织 Skill 文件。

#### Scenario: OpenCode 格式
- **WHEN** 部署 Skills 时
- **THEN** 每个 Skill 包含一个 SKILL.md 文件，描述触发条件、能力和示例

#### Scenario: Skill 目录结构
- **WHEN** 组织 Skill 文件时
- **THEN** 结构为 `skills/opencode/<skill-name>/SKILL.md`
