# [项目名] — Agent 操作卡

> 职责：三个核心文件的浓缩 + 当前路径索引 + Agent 行为约束。
> 保持简短。详细内容去对应文件找。

---

## 核心文件

| 文件 | 职责 | 更新时机 |
|------|------|---------|
| `CONTEXT.md` | Why — 调研、背景、决策依据 | 重大决策时 |
| `DESIGN.md` | What — 业务澄清、技术架构、工程规范 | 加功能/改架构时 |
| `PLAN.md` | How — Phase 列表 + 验收标准 | 开始/完成 Phase 时 |

---

## 当前状态

- 阶段：[设计中 / Phase 1 进行中 / Phase N 完成]
- 下一步：[具体任务]

---

## 路径索引

```
[项目根]/
├── backend/        [后端入口描述]
├── ui/             [前端入口描述]
├── eval/           [测评场景文件]
└── .env            [API keys，gitignored]
```

- AI 测评工具：`~/gh-projects/infra-model-eval/run.sh`
- 验证过的 prompt：`eval/scenarios.yaml`

---

## Agent 行为约束

- 不修改 CONTEXT.md，除非用户明确要求
- 不修改 DESIGN.md 架构部分，除非用户明确要求
- 每个 Phase 完成后跑验收 checklist，通过才进下一个
- prompt 改动后必须跑 infra-model-eval 验证
- `.env` 不提交 git
- 文档和代码同一个 commit 提交
