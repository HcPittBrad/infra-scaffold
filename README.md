# infra-scaffold

AI 工程脚手架。新项目从这里复制，按职责分工填写四个文件。

## 核心范式

```
痛点（CONTEXT）→ 功能点（DESIGN）→ 执行步骤（PLAN）
```

每个文件有一条主线，其余内容是辅助：

| 文件 | 主线（灵魂） | 辅助内容 |
|------|------------|---------|
| `CONTEXT.md` | **核心痛点** | 用户画像、约束、技术决策、淘汰方案 |
| `DESIGN.md` | **功能点清单** | 架构、API契约、数据模型、工程规范 |
| `PLAN.md` | **可验收的执行步骤** | 命令、配置、测试策略 |
| `CLAUDE.md` | **AI 协作规则** | 路径索引、当前状态、行为约束 |

## 使用方式

```bash
cp -r ~/gh-projects/infra-scaffold ~/gh-projects/my-new-project
cd ~/gh-projects/my-new-project
git init && git add . && git commit -m "init: from infra-scaffold"
```

然后按顺序填写：

```
1. CONTEXT.md  先写痛点，写不清楚停下来，写清楚了再继续
2. DESIGN.md   每个功能点对应一个痛点，不能对应的砍掉
3. PLAN.md     每个 Phase 必须有验收标准，没有不算完成
4. CLAUDE.md   填当前状态和路径，保持简短
```

## 填写检验

填完每个文件后，问自己一个问题：

| 文件 | 检验问题 |
|------|---------|
| CONTEXT | 能用一句话说清楚用户在什么场景遭遇了什么痛苦吗？ |
| DESIGN | 每个功能点都能说出它解决了哪个痛点吗？ |
| PLAN | 每个 Phase 结束后，怎么证明它完成了？ |
| CLAUDE.md | AI agent 读完这个文件，知道现在该干什么吗？ |

## 更新时机

| 文件 | 更新触发 | 频率 |
|------|---------|------|
| CONTEXT | 痛点认知变化、重大决策 | 极低 |
| DESIGN | 加功能、改 API、改架构 | 中 |
| PLAN | 开始/完成 Phase | 高 |
| CLAUDE.md | 阶段切换、决策更新 | 低 |

## 提交规范

```
设计阶段    CONTEXT + DESIGN 一起提交
执行阶段    PLAN + 代码 一起提交
架构变更    DESIGN + PLAN + 代码 一起提交
重大决策    CONTEXT + DESIGN 一起提交（说明影响）
```

## 配套工具

- AI 测评：`~/gh-projects/infra-model-eval/`
