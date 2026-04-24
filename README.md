# ai-scaffold

AI 工程脚手架。新项目从这里复制，不从零开始。

## 使用方式

```bash
cp -r ~/gh-projects/ai-scaffold ~/gh-projects/my-new-project
cd ~/gh-projects/my-new-project
git init && git add . && git commit -m "init: from ai-scaffold"
```

然后按顺序填写：

1. `CONTEXT.md` — 先想清楚为什么做，填决策背景
2. `DESIGN.md` — 再想清楚做什么，填架构和接口
3. `PLAN.md` — 最后想清楚怎么做，填执行步骤
4. `CLAUDE.md` — 更新项目名和当前状态

## 文件职责

```
CLAUDE.md   ← AI agent 自动读，onboarding 卡片，保持简短
CONTEXT.md  ← Why，决策背景，重大决策时更新
DESIGN.md   ← What，架构契约，加功能时更新
PLAN.md     ← How，执行步骤，开发期高频更新
README.md   ← 对外介绍 + 最终验收标准
```

## 配套工具

- AI 测评：`~/gh-projects/infra-model-eval/`
- 使用方式：`~/gh-projects/infra-model-eval/README.md`

## 迭代姿势

```
识别要做什么（人）
  → 更新 DESIGN.md（如有架构变化）
  → 更新 PLAN.md（加任务）
  → AI agent 执行
  → 验收（接口测试 + 人工点一遍）
  → 文档 + 代码一起 commit
  → 重要决策 → 更新 CONTEXT.md
```

## 关注点

| 关注什么 | 工具 |
|---------|------|
| 核心文件 | CLAUDE / CONTEXT / DESIGN / PLAN |
| 项目骨架 | 目录结构 + 模块边界 + API 契约 |
| AI 效果验证 | infra-model-eval + scenarios.yaml |
| 接口验证 | curl checklist（PLAN.md Phase N 验收）|
| 集成验证 | 人工关键路径点一遍 |
| 最终验收 | README 能对外讲清楚 |
