# 领域文档

工程技能在探索代码库时，按以下规则读取本仓库的领域文档。

## 探索前读取

- 根目录的 **`CONTEXT.md`**；或
- 若存在根目录 **`CONTEXT-MAP.md`**，则按其指向读取与当前主题相关的各个 `CONTEXT.md`；以及
- **`docs/adr/`**：读取与当前工作区域相关的 ADR。在多上下文仓库中，也检查 `src/<context>/docs/adr/` 的上下文专属决策。

若这些文件尚不存在，继续工作即可。`/domain-modeling` 技能会在术语或决策实际明确后再创建它们。

## 文件布局

本仓库采用单一上下文布局：

```
/
├── CONTEXT.md
├── docs/adr/
│   ├── 0001-event-sourced-orders.md
│   └── 0002-postgres-for-write-model.md
└── src/
```

多上下文布局仅在根目录存在 `CONTEXT-MAP.md` 时使用：

```
/
├── CONTEXT-MAP.md
├── docs/adr/                          ← 系统级决策
└── src/
    ├── ordering/
    │   ├── CONTEXT.md
    │   └── docs/adr/                  ← 上下文专属决策
    └── billing/
        ├── CONTEXT.md
        └── docs/adr/
```

## 使用术语表词汇

在 Issue 标题、重构提案、假设和测试名称中提及领域概念时，使用 `CONTEXT.md` 中定义的术语，避免改用其明确排除的同义词。

需要的概念若尚未出现在术语表中，先判断是否误用了项目未采用的语言；若确为领域缺口，则记录给 `/domain-modeling`。

## 标出 ADR 冲突

输出与既有 ADR 矛盾时，明确指出冲突，而非静默覆盖。例如：

> _与 ADR-0007（订单事件溯源）冲突，但值得重新审视，因为……_
