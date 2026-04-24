# [项目名] — Agent 操作卡（灵魂：AI 协作规则）

> 一句话定位：**告诉 AI agent 当前项目的操作规则，让它不问背景直接开干。**
> 这是三个核心文件的浓缩入口 + 行为约束，保持简短。详细内容去对应文件找。

---

## 范式（必读）

```
痛点（CONTEXT）→ 功能点（DESIGN）→ 执行步骤（PLAN）
```

- **CONTEXT.md**：为什么做，核心痛点是什么
- **DESIGN.md**：做什么，功能点清单是主线
- **PLAN.md**：怎么做，可验收的 Phase 是主线
- **这个文件**：AI 怎么配合，操作规则

---

## 当前状态

- 阶段：[设计中 / Phase N 进行中 / Phase N 完成，待进入 Phase N+1]
- 下一步：[具体任务，agent 读完直接知道该干什么]
- 最近决策：[影响当前工作的最新决定]

---

## 路径索引

```
[项目根]/
├── CONTEXT.md       ← 痛点和决策背景
├── DESIGN.md        ← 功能点清单和架构
├── PLAN.md          ← Phase 执行计划
├── backend/         ← [后端说明]
├── ui/              ← [前端说明]
├── eval/            ← prompt 验证场景
│   └── scenarios.yaml
└── .env             ← API keys（gitignored）
```

关联工具：
- AI 测评：`~/gh-projects/infra-model-eval/run.sh`
- 验证过的 prompt：`eval/scenarios.yaml`

---

## Agent 行为约束

- **读顺序**：先读此文件 → CONTEXT → DESIGN → PLAN，再动手
- 不修改 CONTEXT.md 架构决策部分，除非用户明确要求
- 不修改 DESIGN.md 功能点和 API 契约，除非用户明确要求
- 每个 Phase 完成后跑验收 checklist，**通过才进下一个**
- prompt 改动后必须跑 infra-model-eval 验证，不验证不提交
- `.env` 不提交 git
- 文档和代码同一个 commit 提交
