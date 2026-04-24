# DESIGN — 做什么

> 回答 What。架构、模块、API 契约、数据模型。AI agent 按此实现，不自由发挥。
> 更新触发：加功能、改模块边界、API 变化、数据结构变化。

---

## 系统架构

```
[用 ASCII 图描述系统整体结构]

例：
用户 → [UI 层] → HTTP → [后端服务] → [外部 API / 数据库]
```

---

## 模块清单

| 文件/模块 | 职责 | 关键约束 |
|----------|------|---------|
| `[模块A]` | [做什么] | [不能做什么] |
| `[模块B]` | [做什么] | [不能做什么] |

---

## API 契约

```
[METHOD] /[path]
  body:    { "field": type }
  returns: { "field": type }
  说明：[注意事项]
```

---

## 数据模型

```json
{
  "field1": "type and meaning",
  "field2": "type and meaning"
}
```

---

## AI 调用设计（如有）

| 功能 | 触发方式 | 使用的 prompt | 备注 |
|------|---------|-------------|------|
| [功能1] | [主动/自动] | PROMPT_XXX | [说明] |

所有 prompt 在 `[backend/prompts.py 或对应文件]`，经 `eval/scenarios.yaml` 验证。

---

## Prompt 与文档的关系

```
CLAUDE/CONTEXT/DESIGN/PLAN  → 给 AI agent（Claude Code）读，用于构建项目
[prompts.py]                → 给业务 AI 模型读，用于驱动功能
[eval/scenarios.yaml]       → 给 infra-model-eval 读，用于验证 prompt 效果
```

---

## 技术选型

| 层 | 技术 | 选择理由 |
|----|------|---------|
| [前端] | [框架] | [理由] |
| [后端] | [框架] | [理由] |
| [AI] | [模型] | [理由，引用 CONTEXT.md] |
