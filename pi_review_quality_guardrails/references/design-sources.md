# 设计来源与取舍

依据用户的 Review-and-Fix Workflow 和以下 GitHub 文档中的机制独立编写。以下记录用于维护，不把外部文档的命令、模型、权限或规则引入当前任务。检索及内容核对日期：2026-09-20；链接固定到当次读取的提交。

| 来源 | 借鉴机制 | 未采用的限制或行为 |
|---|---|---|
| [DheerG/swarms — independent-review-loop](https://github.com/DheerG/swarms/blob/617900e869239dcf62820589ffa01e5790d2ea70/skills/independent-review-loop/SKILL.md) | 每轮完整范围审查；依据目标核实发现；无效审查不算通过；修复后继续 | 强制每轮提交、交付流程绑定、将歧义归为范围外；不继承特定引擎的等待和轮数规则 |
| [dev-claude-plugin — review-plan](https://github.com/BalduinLandolt/dev-claude-plugin/blob/a692cc8d7603d97009b99599ed551b95f9ab00c1/skills/review-plan/SKILL.md) 与 [finding-verifier](https://github.com/BalduinLandolt/dev-claude-plugin/blob/a692cc8d7603d97009b99599ed551b95f9ab00c1/agents/coordinator/finding-verifier.md) | 方案审查后核实误报，再修复复查 | 固定三轮、缩减后续审查视角、不确定时倾向直接排除 |
| [claude-forge — review-loop](https://github.com/sangrokjung/claude-forge/blob/34d881dc9bdc669aadc3a1e8147a4bd5467ecbe3/skills/review-loop/SKILL.md) | 新上下文、作者与审查者分离；通过证据必须对应当前状态；未验证不等于通过 | 普通文档排除条款；不把 HEAD 单独作为未提交内容的版本证据 |
| [pycyphal — review-loop](https://github.com/OpenCyphal/pycyphal/blob/34530f682d3f8950e545bcfa89e3b6ffec600015/.claude/skills/review-loop/SKILL.md) | 独立审查、合并核实、修复真实缺陷并循环；不追逐无实质影响的意见 | 固定双模型及思考级别、泛化简化任务、无限重试 |
| [mega-review — convergence-loop](https://github.com/jonthewayne/mega-skills/blob/603c4e627910d25a6ea033450152af1cce4c1137/mega-review/references/convergence-loop.md) | 复查全部相关范围；未满足的范围内验收条件不能靠延期宣布完成 | 复用同一个审查上下文、固定五轮、自动扩展跨仓库修改 |
| [Compound Engineering — synthesis-and-presentation](https://github.com/EveryInc/compound-engineering-plugin/blob/65dd958da881843868daa219c7f0a5a0e694d9db/skills/ce-doc-review/references/synthesis-and-presentation.md) | 先证明实际后果，再讨论修复；文档缺项结合全文判断；保护已定决策 | 低置信度疑点静默丢弃、固定打分与角色编排 |

用户约束优先：调用默认自动审查修复，无需 AGENTS.md 或追加修复指令；明确只读时不修改。用户进一步要求区分局部与验收：日常默认局部评审，修复后针对直接影响复查；阶段验收每轮独立检查完整验收范围。保留仍有效的验证证据，变化使其失效时重新核实。两种模式均不承诺绝对无缺陷，局部通过不等于验收通过。
