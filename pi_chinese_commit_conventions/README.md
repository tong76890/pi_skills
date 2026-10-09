# pi_chinese_commit_conventions：中文 Git 提交规范

为 Git 提交生成符合 Conventional Commits 的中文提交信息，适用于需要提交代码或文档、执行 `git commit`，或生成、修改提交信息的场景。

## 使用

在 Codex 中发送：

```text
使用 $pi_chinese_commit_conventions 根据当前 Git 差异生成提交信息。
```

技能会根据实际差异选择 `feat`、`fix`、`docs`、`refactor` 等提交类型；复杂或有影响范围的改动会在正文中说明背景、具体改动、影响范围和验证情况。

## 约定

- 提交类型使用英文 Conventional Commits 关键字。
- scope 和 subject 使用中文，subject 使用动宾短语且不以句号结尾。
- 涉及数据库、公共 API 或配置格式的不兼容变更，必须标注 `BREAKING CHANGE`。
- 详细规则与示例见 [SKILL.md](SKILL.md)。

## 安装与依赖

按仓库首页的 [Codex 安装说明](../README.md#安装到-codex) 安装 `pi_chinese_commit_conventions`。

安装技能后，将以下规则追加到 Codex 生效的全局指令文件，保留已有内容：默认目录为 `~/.codex/`；设置了 `CODEX_HOME` 时使用 `$CODEX_HOME/`。若同目录的 `AGENTS.override.md` 存在且非空，Codex 会优先读取它，将规则追加到该文件；否则将规则追加到 `AGENTS.md`，文件不存在时创建。

```markdown
## Git 提交规范

执行 Git 提交或生成、修改提交信息时，必须使用 `$pi_chinese_commit_conventions` 技能。
该技能不可用时，明确报告并暂停这些操作。
```

该配置由安装者手动加入；仅复制技能目录不会自动修改 `AGENTS.md`。详见 [Codex `AGENTS.md` 说明](https://developers.openai.com/codex/guides/agents-md)。

生成提交信息时需要能查看实际 Git 差异；执行提交需要 Git。技能中的 commitlint、Husky 和 changelog 配置是可选落地示例，使用这些示例需要 Node.js、npm 及对应依赖；安装技能不会自动安装或配置这些工具。
