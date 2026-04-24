# infra-scaffold

AI 工程脚手架。新项目从这里复制，按职责分工填写四个文件。

## 使用方式

```bash
cp -r ~/gh-projects/infra-scaffold ~/gh-projects/my-new-project
cd ~/gh-projects/my-new-project
git init && git add . && git commit -m "init: from infra-scaffold"
```

## 四文件职责（单一职责）

```
CONTEXT.md   Why    调研 + 项目背景 + 重大决策依据
DESIGN.md    What   业务澄清 + 技术架构 + 工程规范（业务侧）
PLAN.md      How    Phase 列表 + 每步验收标准
CLAUDE.md    Ops    三者浓缩 + 路径索引 + Agent 行为约束
```

## 填写顺序

```
CONTEXT → DESIGN → PLAN → CLAUDE.md → 工程结构 → 代码
  Why      What     How     Agent卡     骨架        血肉
```

## 更新时机

| 文件 | 更新触发 | 频率 |
|------|---------|------|
| CONTEXT | 重大决策（换模型、改方向） | 极低 |
| DESIGN | 加功能、改架构 | 中 |
| PLAN | 开始/完成 Phase | 高 |
| CLAUDE.md | 项目阶段切换 | 低 |

## 提交规范

```
设计阶段    CONTEXT + DESIGN 一起提交（无代码）
执行阶段    PLAN + 代码 一起提交
架构变更    DESIGN + PLAN + 代码 一起提交
重大决策    CONTEXT + DESIGN 一起提交
```

## 配套工具

- AI 测评：`~/gh-projects/infra-model-eval/`
- 使用文档：`~/gh-projects/infra-model-eval/README.md`
