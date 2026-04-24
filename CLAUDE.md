# [项目名] — Agent Context

> AI agent 进入目录自动读此文件。保持简短，指向其他文件，记录当前状态。

## 核心文件导航

| 文件 | 回答 | 优先级 |
|------|------|-------|
| `CONTEXT.md` | 为什么这么做，决策背景 | 先读 |
| `DESIGN.md` | 做什么，架构和接口 | 再读 |
| `PLAN.md` | 怎么做，执行步骤 | 执行时读 |

## 当前状态

- 阶段：[设计中 / Phase 1 实现中 / Phase N 完成 / 已上线]
- 下一步：[具体任务描述]
- 最近决策：[一句话说明最近的重大决策]

## 快速信息

- 技术栈：[前端/后端/语言]
- AI 模型：[模型名]，key 在 `.env`
- 测评工具：`~/gh-projects/infra-model-eval/`

## Agent 规则

- 不得修改 CONTEXT.md，除非用户明确要求
- 每个 PLAN.md 阶段完成后运行验收 checklist
- `.env` 不提交
- prompt 改动后必须跑 infra-model-eval 验证
