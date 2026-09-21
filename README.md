# pi_skills

pi 维护的 **Codex 技能集合**。一个文件夹对应一个 skill，各技能独立安装、更新和使用，按需选择即可。目前仅适配 Codex。

## 技能目录

| 技能 | 用途 | 使用说明 | 调用 |
| --- | --- | --- | --- |
| pi_ui | Qt、MFC/Win32、Web 界面自定义新增或修改的交付前审查 | [说明](pi_ui/README.md) · [规则](pi_ui/SKILL.md) | `$pi_ui` |
| pi_review_quality_guardrails | 自动开发中的局部审查修复、goal 完成前独立验收 | [说明](pi_review_quality_guardrails/README.md) · [规则](pi_review_quality_guardrails/SKILL.md) | `$pi_review_quality_guardrails` |

新增技能会持续加入此表。每个技能的适用范围、依赖和示例以其目录内说明为准。

## 仓库约定

```text
pi_skills/
├── README.md              # 技能目录、通用安装和使用方式
└── pi_ui/                 # 一个目录就是一个 skill
    ├── SKILL.md           # 必需：元数据与执行规则
    ├── README.md          # 面向使用者的说明
    ├── agents/            # 可选：Codex 界面元数据
    └── references/        # 可选：详细参考资料
```

后续技能与 `pi_ui/` 平级存放。安装单位是包含 `SKILL.md` 的完整文件夹，不要把整个 `pi_skills/` 当成一个技能安装，也不要只复制 `SKILL.md`。

## 安装到 Codex

先从技能目录选择所需技能。以下用 `pi_ui` 作当前示例；安装其他技能时替换为对应目录名。

### 方式一：让 Codex 安装

在 Codex 中发送（将 `技能目录名` 替换成实际名称）：

```text
使用 $skill-installer 从 https://github.com/tong76890/pi_skills/tree/main/技能目录名 安装该技能。
```

例如：

```text
使用 $skill-installer 从 https://github.com/tong76890/pi_skills/tree/main/pi_ui 安装 pi_ui。
```

安装多个技能时，可在同一请求中列出要安装的目录名。由当前 Codex 自带安装器选择安装位置；若提示已存在，先确认现有版本是否有个人修改，再决定更新。

### 方式二：手动安装一个或多个技能

需要 Git。先在用于保存仓库的目录克隆一次：

```sh
git clone https://github.com/tong76890/pi_skills.git
```

克隆成功后在同一目录执行以下命令；已有仓库副本时无需重复克隆。安装列表仅填写技能目录中实际存在的名称。

按当前 [Codex 官方文档](https://developers.openai.com/codex/skills/)，用户级技能放在 `~/.agents/skills/`，可供多个项目使用。

Windows PowerShell：

```powershell
$skillNames = @('pi_ui') # 可填写多个名称，例如 @('名称一', '名称二')
$skillsRoot = Join-Path $HOME '.agents/skills'
# 先检查全部选项，避免因已有安装而覆盖个人修改。
foreach ($skillName in $skillNames) {
    $skillSource = Join-Path './pi_skills' $skillName
    $skillTarget = Join-Path $skillsRoot $skillName
    if (-not (Test-Path -LiteralPath (Join-Path $skillSource 'SKILL.md'))) {
        throw "找不到技能：$skillName"
    }
    if (Test-Path -LiteralPath $skillTarget) {
        throw "技能已存在，请先按更新说明处理：$skillName"
    }
}
New-Item -ItemType Directory -Path $skillsRoot -Force | Out-Null
foreach ($skillName in $skillNames) {
    Copy-Item -LiteralPath (Join-Path './pi_skills' $skillName) `
        -Destination (Join-Path $skillsRoot $skillName) -Recurse
}
```

macOS / Linux：

```sh
(
  set -e
  set -- pi_ui # 可填写多个技能目录名，以空格分隔
  skills_root="$HOME/.agents/skills"
  for skill_name do
    if [ ! -f "./pi_skills/$skill_name/SKILL.md" ]; then
      echo "找不到技能：$skill_name" >&2
      exit 1
    fi
    if [ -e "$skills_root/$skill_name" ] || [ -L "$skills_root/$skill_name" ]; then
      echo "技能已存在，请先按更新说明处理：$skill_name" >&2
      exit 1
    fi
  done
  mkdir -p "$skills_root"
  for skill_name do
    cp -R "./pi_skills/$skill_name" "$skills_root/$skill_name"
  done
)
```

### 仅供某个项目使用

将选中的完整技能文件夹复制到目标项目的 `.agents/skills/` 下，每个技能单独一个子目录：

```text
你的项目/.agents/skills/技能目录名/SKILL.md
```

从目标项目启动 Codex。同一个技能通常只选择用户级或项目级安装；同名技能不会自动合并，重复安装可能产生多个条目。

### 已有安装与识别

Codex 会自动发现技能变化；如果未出现，重新启动后检查技能列表。

部分已有环境的安装器使用 `$CODEX_HOME/skills`（默认 `~/.codex/skills`）。若该位置已有可用技能，沿用实际安装位置更新即可，避免在另一目录重复安装。

## 通用使用方式

在 Codex 请求中写出 `$技能名`，并描述具体任务。技能名取自对应 `SKILL.md` 的 `name`，本仓库保持它与目录名一致。

Codex 也可按描述自动选择允许隐式调用的技能；显式指定名称更便于确定本次使用哪个技能。具体调用时机、任务边界与所需运行环境，查看技能目录表中的使用说明。

技能提供工作规则，不是自动执行的 CI 门禁。安装技能不等于安装它可能需要的编译器、浏览器或其他验证工具，也不能保证检查没有遗漏。

## 更新与卸载

更新仓库副本：

```sh
git -C pi_skills pull --ff-only
```

手动复制的安装不会自动同步。只更新你选中的技能：先备份个人修改，将该技能的旧目录移出技能扫描位置，再将仓库中的完整新目录复制到原位置。其他已安装技能无需改动，不要只覆盖单个 `SKILL.md`。

卸载时仅移除目标技能的安装目录，保留其他技能和仓库副本。若仍出现旧条目，重启 Codex 并检查是否存在同名副本。

## 添加新技能

1. 在仓库根目录创建独立的 `pi_名称/` 文件夹。
2. 添加 `SKILL.md`，包含一致的 `name`、准确的 `description` 和执行规则。
3. 添加面向使用者的 `README.md`，说明用途、触发条件、调用示例和实际依赖。
4. 按需添加 `agents/`、`references/`、`scripts/` 或 `assets/`，不创建无用占位文件。
5. 更新首页技能目录表。所有技能专用文件与相对引用保留在自身目录中；使用说明可链接到首页通用安装文档。

## 兼容说明

本仓库保留 `pi_` 前缀。`pi_ui` 已在作者当前 Codex 环境中被发现；部分创建工具的校验器只接受连字符命名，会对下划线报错，这是已知命名兼容差异，不代表通过所有版本或第三方工具验证。

当前仅维护 Codex 适配。安装目录与发现机制以 [官方文档](https://developers.openai.com/codex/skills/) 和实际使用版本为准。
