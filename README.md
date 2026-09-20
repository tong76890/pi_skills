# pi_skills

维护 pi 编写的 AI skills，目前仅适配 **Codex**。每个根目录下的技能文件夹都是一个可独立安装的 skill。

## 技能列表

| 技能 | 用途 | 调用 |
| --- | --- | --- |
| [pi_ui](pi_ui/SKILL.md) | 对 Qt、MFC/Win32 和 Web 界面的自定义新增或修改执行交付前审查 | `$pi_ui` |

## 目录结构

```text
pi_skills/
├── README.md
└── pi_ui/
    ├── SKILL.md                 # 触发条件、验收流程与结论
    ├── agents/
    │   └── openai.yaml          # Codex 界面元数据
    └── references/
        └── checklist.md        # 详细验收清单
```

安装时复制整个技能文件夹，不能只复制 `SKILL.md`。后续技能按相同方式新增独立目录，不需要安装整个仓库。

## 安装到 Codex

任选一种方式。请先安装可使用 skills 的 Codex；命令行复制方式还需要 Git。

### 方式一：让 Codex 安装

在 Codex 中发送：

```text
使用 $skill-installer 从 https://github.com/tong76890/pi_skills/tree/main/pi_ui 安装 pi_ui 技能。
```

由当前 Codex 自带的安装器选择安装位置。如果提示技能已经存在，请先确认现有版本是否有个人修改，再决定更新，不要重复安装。

### 方式二：手动安装到用户目录

按当前 [Codex 官方技能文档](https://developers.openai.com/codex/skills/)，用户级技能放在 `~/.agents/skills/`，可供不同项目使用。

Windows PowerShell，在用于保存仓库的目录执行：

```powershell
git clone https://github.com/tong76890/pi_skills.git
if ($LASTEXITCODE -ne 0) { throw '仓库克隆失败' }
$skillsRoot = Join-Path $HOME '.agents/skills'
$skillTarget = Join-Path $skillsRoot 'pi_ui'
if (Test-Path -LiteralPath $skillTarget) {
    throw 'pi_ui 已存在，请按更新说明处理，避免覆盖个人修改。'
}
New-Item -ItemType Directory -Path $skillsRoot -Force | Out-Null
Copy-Item -LiteralPath './pi_skills/pi_ui' -Destination $skillTarget -Recurse
```

macOS / Linux，在用于保存仓库的目录执行：

```sh
git clone https://github.com/tong76890/pi_skills.git && (
  set -e
  skills_root="$HOME/.agents/skills"
  skill_target="$skills_root/pi_ui"
  if [ -e "$skill_target" ] || [ -L "$skill_target" ]; then
    echo 'pi_ui 已存在，请按更新说明处理，避免覆盖个人修改。' >&2
    exit 1
  fi
  mkdir -p "$skills_root"
  cp -R ./pi_skills/pi_ui "$skill_target"
)
```

如果已有仓库副本，直接使用其中的 `pi_ui/`，无需重复克隆。

### 仅在一个项目中使用

将整个 `pi_ui/` 文件夹复制到目标项目的 `.agents/skills/` 下，得到：

```text
你的项目/.agents/skills/pi_ui/SKILL.md
```

从该项目启动 Codex。用户级与项目级安装通常选择一种即可；同名技能不会自动合并，重复安装可能在技能列表中显示多个条目。

### 确认识别

Codex 会自动发现技能变化；如果没有出现，重新启动 Codex，再尝试输入 `$pi_ui`。

已有环境可能由安装器将技能放在 `$CODEX_HOME/skills`（默认 `~/.codex/skills`）。若那里已有可用的 `pi_ui`，沿用其实际安装位置更新即可，不必再复制到 `.agents/skills` 形成重复安装。

## 使用 pi_ui

完成本次约定功能及相关功能验证后，在 Codex 中发送：

```text
使用 $pi_ui 审查本次界面的自定义新增和修改。
只检查变更及受影响区域；本次仅审查，不修改代码。
```

需要检查并修复时：

```text
使用 $pi_ui 对本次界面变更做交付前验收，修复有明确依据的问题，
复验受影响部分，并报告通过、未通过或待验证。
```

技能默认允许自动发现，描述匹配时 Codex 也可以自行选择它；明确写出 `$pi_ui` 能更直接地指定本次使用。技能是工作指导，不是自动执行的 CI 门禁，也不保证所有视觉缺陷都能被发现。

### 何时检查

- 自定义新增界面或控件，修改布局、尺寸、间距、字体、图标呈现、样式、绘制或交互时，必须审查；有模板也不豁免，微调同样适用。
- 完整沿用模板设计、仅通过既定参数替换内容时，走常规显示与功能验证。
- 功能开发阶段保持轻量，交付前集中验收，避免以反复美化代替功能实现。
- 纯界面或原型任务按约定目标验收，不补做范围外业务。

### 检查什么

检查控件、文字、图标的完整性与可见性，风格与尺寸、文字及控件对齐、间距、裁切遮挡，以及相关操作状态。详见 [验收清单](pi_ui/references/checklist.md)。

检查需要实际运行界面的观察或可确认对应本次变更的运行截图；交互需要实际操作。技能不附带桌面自动化或浏览器工具，不会自动获得运行环境。请让 Codex 使用项目已有的构建、启动与验证方式；浏览器工具不能替代 Qt/MFC 原生窗口验证。

### 如何判定

| 结论 | 条件 |
| --- | --- |
| 未通过 | 已发现未解决的验收缺陷，即使同时还有未验证项 |
| 待验证 | 没有已知未解决缺陷，但缺少必要环境或证据 |
| 通过 | 适用检查完成，且没有未解决的验收缺陷 |

专项通过不等于整个任务通过，不替代功能测试、代码审查或项目质量要求。同一版本、环境和状态下仍有效的证据可以复用，避免重复验证。

## 更新与卸载

更新仓库副本：

```sh
git -C pi_skills pull --ff-only
```

手动安装不会随仓库更新自动同步。先备份已安装技能中的个人修改，将旧 `pi_ui/` 移出技能扫描目录，再将仓库中的完整 `pi_ui/` 复制到原安装位置。不要只覆盖 `SKILL.md` 而遗漏引用文件。

卸载时，仅移除实际安装位置中的 `pi_ui/` 文件夹；仓库副本可以保留。若 Codex 仍显示旧条目，重新启动后检查是否还有同名副本。

## 命名与兼容说明

本仓库保留 `pi_` 前缀，目录名与 `SKILL.md` 的 `name` 一致。`pi_ui` 已在作者当前 Codex 环境中被发现；部分技能创建工具的校验器只接受连字符命名，会对下划线报错。这是已知的命名兼容差异，不代表已经通过所有版本或第三方工具的验证。

当前不提供其他 AI 工具的安装配置，也不要求安装额外技能包。Codex 安装目录与发现机制以 [官方文档](https://developers.openai.com/codex/skills/) 和实际使用版本为准。
