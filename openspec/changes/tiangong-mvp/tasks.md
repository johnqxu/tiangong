# 天工 MVP - 实现任务清单

## 1. 项目初始化

- [ ] 1.1 创建项目目录结构 (backend/, skills/, templates/)
- [ ] 1.2 初始化 Python 项目 (pyproject.toml, requirements.txt)
- [ ] 1.3 创建 FastAPI 应用骨架 (main.py, config.py)
- [ ] 1.4 配置 SQLAlchemy 数据库连接
- [ ] 1.5 配置 Redis 连接
- [ ] 1.6 创建 Docker Compose 配置 (api, db, redis)
- [ ] 1.7 创建 Dockerfile
- [ ] 1.8 配置 Alembic 数据库迁移

## 2. 数据模型

- [ ] 2.1 创建基础模型类 (Base, TimestampMixin)
- [ ] 2.2 实现 Department 模型
- [ ] 2.3 实现 Group 模型
- [ ] 2.4 实现 Member 模型
- [ ] 2.5 实现 SkillCategory 模型
- [ ] 2.6 实现 Skill 模型 (支持层级)
- [ ] 2.7 实现 MemberSkill 模型
- [ ] 2.8 实现 Project 模型
- [ ] 2.9 实现 Task 模型 (支持树形 WBS)
- [ ] 2.10 实现 Assignment 模型
- [ ] 2.11 创建初始数据库迁移脚本

## 3. Pydantic Schemas

- [ ] 3.1 创建组织相关 Schema (Department, Group, Member)
- [ ] 3.2 创建技能相关 Schema (SkillCategory, Skill, MemberSkill)
- [ ] 3.3 创建项目相关 Schema (Project, ProjectCreate, ProjectUpdate)
- [ ] 3.4 创建任务相关 Schema (Task, TaskCreate, TaskUpdate, TaskTree)
- [ ] 3.5 创建分配相关 Schema (Assignment, AssignmentCreate)
- [ ] 3.6 创建匹配相关 Schema (MatchRequest, MatchResult, Candidate)
- [ ] 3.7 创建洞察相关 Schema (Saturation, Timeline, SkillSupply)
- [ ] 3.8 创建通用响应 Schema (Pagination, ErrorResponse)

## 4. 组织管理 API

- [ ] 4.1 实现部门 CRUD API (GET, POST, PUT, DELETE /departments)
- [ ] 4.2 实现小组 CRUD API (GET, POST, PUT, DELETE /groups)
- [ ] 4.3 实现人员 CRUD API (GET, POST, PUT, DELETE /members)
- [ ] 4.4 实现组织架构树查询 API (GET /org/tree)
- [ ] 4.5 实现按角色筛选成员 API (GET /members?role=xxx)

## 5. 技能管理 API

- [ ] 5.1 实现技能分类 CRUD API
- [ ] 5.2 实现技能 CRUD API (支持层级)
- [ ] 5.3 实现人员技能管理 API (GET, PUT /members/{id}/skills)
- [ ] 5.4 实现技能树查询 API (GET /skills/tree)
- [ ] 5.5 实现按技能查询人员 API (GET /skills/{id}/members)

## 6. 项目管理 API

- [ ] 6.1 实现项目 CRUD API
- [ ] 6.2 实现项目状态更新 API
- [ ] 6.3 实现按状态/业务线筛选项目 API
- [ ] 6.4 实现项目统计 API (GET /projects/{id}/stats)

## 7. 任务与 WBS API

- [ ] 7.1 实现任务 CRUD API (支持树形结构)
- [ ] 7.2 实现任务需求定义 API (角色、技能、工时、时间窗口)
- [ ] 7.3 实现任务状态流转 API (publish, complete, pause)
- [ ] 7.4 实现项目 WBS 树查询 API (GET /projects/{id}/tasks)
- [ ] 7.5 实现任务进度更新 API

## 8. 资源匹配引擎

- [ ] 8.1 实现角色匹配评分函数
- [ ] 8.2 实现技能匹配评分函数 (含父技能降级匹配)
- [ ] 8.3 实现时间可用度计算函数
- [ ] 8.4 实现综合匹配评分函数 (加权平均)
- [ ] 8.5 实现候选人推荐 API (POST /matching/match)
- [ ] 8.6 实现批量匹配 API

## 9. 资源分配 API

- [ ] 9.1 实现分配创建 API (POST /assignments)
- [ ] 9.2 实现分配确认 API (POST /assignments/{id}/confirm)
- [ ] 9.3 实现分配取消 API (DELETE /assignments/{id})
- [ ] 9.4 实现分配时间分布更新 API
- [ ] 9.5 实现查询成员任务 API (GET /members/{id}/tasks)

## 10. 资源透视引擎

- [ ] 10.1 实现个人饱和度计算函数
- [ ] 10.2 实现小组饱和度计算函数
- [ ] 10.3 实现部门饱和度计算函数
- [ ] 10.4 实现时间轴视图计算函数
- [ ] 10.5 实现技能供需分析函数
- [ ] 10.6 实现资源缺口预测函数
- [ ] 10.7 实现异常检测函数 (过载、闲置、技能不匹配)

## 11. 透视洞察 API

- [ ] 11.1 实现饱和度查询 API (GET /insights/saturation)
- [ ] 11.2 实现时间轴视图 API (GET /insights/timeline)
- [ ] 11.3 实现技能供需 API (GET /insights/skill-supply)
- [ ] 11.4 实现缺口预测 API (GET /insights/forecast)
- [ ] 11.5 实现异常检测 API (GET /insights/alerts)

## 12. 数据导入功能

- [ ] 12.1 创建组织架构导入 Excel 模板
- [ ] 12.2 创建人员信息导入 Excel 模板
- [ ] 12.3 创建技能体系导入 Excel 模板
- [ ] 12.4 创建项目任务导入 Excel 模板
- [ ] 12.5 实现模板下载 API (GET /import/templates)
- [ ] 12.6 实现组织架构导入解析逻辑
- [ ] 12.7 实现人员信息导入解析逻辑
- [ ] 12.8 实现技能体系导入解析逻辑
- [ ] 12.9 实现项目任务导入解析逻辑
- [ ] 12.10 实现数据校验逻辑
- [ ] 12.11 实现批量导入执行 API (POST /import/execute)
- [ ] 12.12 实现导入结果反馈 API

## 13. OpenCode Skills

- [ ] 13.1 创建 Skill 目录结构 (skills/opencode/)
- [ ] 13.2 实现 tiangong-org Skill (组织管理)
- [ ] 13.3 实现 tiangong-skill Skill (技能管理)
- [ ] 13.4 实现 tiangong-project Skill (项目管理)
- [ ] 13.5 实现 tiangong-wbs Skill (WBS 管理 + AI 辅助分解)
- [ ] 13.6 实现 tiangong-match Skill (资源匹配)
- [ ] 13.7 实现 tiangong-insight Skill (透视洞察)
- [ ] 13.8 实现 tiangong-import Skill (数据导入)
- [ ] 13.9 实现 tiangong-report Skill (报告生成)

## 14. 缓存与性能优化

- [ ] 14.1 实现饱和度计算结果缓存
- [ ] 14.2 实现时间轴视图缓存
- [ ] 14.3 实现缓存失效逻辑 (任务/分配变更时)
- [ ] 14.4 优化透视查询 SQL (添加索引)

## 15. 测试

- [ ] 15.1 编写组织管理 API 单元测试
- [ ] 15.2 编写技能管理 API 单元测试
- [ ] 15.3 编写项目管理 API 单元测试
- [ ] 15.4 编写任务 WBS API 单元测试
- [ ] 15.5 编写资源匹配引擎单元测试
- [ ] 15.6 编写透视计算单元测试
- [ ] 15.7 编写数据导入单元测试
- [ ] 15.8 编写 API 集成测试

## 16. 文档与部署

- [ ] 16.1 编写 README.md (项目介绍、安装、使用)
- [ ] 16.2 编写 API 文档 (基于 OpenAPI 自动生成)
- [ ] 16.3 编写部署文档
- [ ] 16.4 编写 Skill 使用文档
- [ ] 16.5 配置生产环境 Docker Compose
- [ ] 16.6 验证部署流程
