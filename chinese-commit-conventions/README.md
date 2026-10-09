# chinese-commit-conventions：中文 Git 提交规范

为 Git 提交生成符合 Conventional Commits 的中文提交信息，适用于需要提交代码或文档、执行 `git commit`，或生成、修改提交信息的场景。

## 使用

在 Codex 中发送：

```text
使用 $chinese-commit-conventions 根据当前 Git 差异生成提交信息。
```

技能会根据实际差异选择 `feat`、`fix`、`docs`、`refactor` 等提交类型；复杂或有影响范围的改动会在正文中说明背景、具体改动、影响范围和验证情况。

## 约定

- 提交类型使用英文 Conventional Commits 关键字。
- scope 和 subject 使用中文，subject 使用动宾短语且不以句号结尾。
- 涉及数据库、公共 API 或配置格式的不兼容变更，必须标注 `BREAKING CHANGE`。
- 详细规则与示例见 [SKILL.md](SKILL.md)。

## 安装与依赖

按仓库首页的 [Codex 安装说明](../README.md#安装到-codex) 安装 `chinese-commit-conventions`。

生成提交信息时需要能查看实际 Git 差异；执行提交需要 Git。技能中的 commitlint、Husky 和 changelog 配置是可选落地示例，使用这些示例需要 Node.js、npm 及对应依赖；安装技能不会自动安装或配置这些工具。
