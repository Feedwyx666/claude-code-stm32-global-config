# Claude Code 全局配置与学习记忆

自学 STM32 嵌入式开发过程中沉淀下来的 Claude Code 工作流配置：一份全局规则（`CLAUDE.md`）加一套可按需召回的记忆（`memory/`）。

目标是让 Claude 在任意目录启动时就已经知道：用中文回答、我在学 STM32 所以要讲原理、改文件前先留回退点、本机 Keil 装在哪、什么目录该建启动脚本、什么目录不该动。不用每次重新交代一遍。

## 仓库内容

| 路径 | 作用 |
|------|------|
| `CLAUDE.md` | 全局启动必加载。9 条规则，其中 4 条无条件生效，5 条带适用条件 |
| `memory/MEMORY.md` | 记忆索引，一行一条指向具体记忆文件 |
| `memory/*.md` | 11 条记忆，带 frontmatter，按需召回 |
| `.gitignore` | 把 `~/.claude/` 里的运行时数据挡在版本控制外 |

## 规则与记忆的分工

两者不是重复关系，是**入口与细节**的关系。

`CLAUDE.md` 每次会话都会完整加载，所以只放**必须立刻知道**的结论：用中文、先留回退点、Keil 命令是什么。它要短，因为它占的是每次对话的开头预算。

`memory/` 里的每条记忆则记录**这条规则为什么存在**——通常是某次踩坑的完整经过。比如 `ensure-launch-script.md` 里记着启动脚本为什么绝不能叫 `claude.bat`（会和 `claude` 命令同名，cmd 在当前目录找到同名 bat 再执行，无限递归弹窗拖垮系统），也记着为什么不能用 `-d "%~dp0"` 给 Windows Terminal 传目录（路径末尾的反斜杠加引号会被解析成转义引号，报 `0x8007010b`）。这些细节平时不需要占用上下文，等真要动启动脚本时再召回。

规则冲突时的优先级：**全局记忆（本机事实）> 工程自带的 `CLAUDE.md`**。旧电脑复制过来的工程 md 里可能写着 `D:\Keil_v5` 这种失效路径，见 `stale-paths-in-project-md.md`。

## 11 条记忆

按类型分：

**用户偏好**
- `use-chinese.md` — 所有回复用中文，代码与日志原文不翻译
- `user-learning-stm32.md` — 交流目的是学 STM32，侧重讲原理而不是只给改动结果
- `confirm-before-edits.md` — 询问语气（「是不是可以」「能不能」）表示在征询意见，先解释再问要不要改

**操作纪律**
- `backup-before-changes.md` — 改动前必留回退点。在 git 仓库内就用提交（有远端一并推送）代替备份，不在仓库才做文件复制
- `read-project-before-answer.md` — 工程目录内回答前先通读用户编辑过的源码，不凭直觉
- `desktop-file-placement.md` — 桌面开了自动排列，新建文件会把所有图标挤错位，默认不往桌面放

**本机事实**
- `keil-build-flash.md` — Keil 在 `C:\Keil_v5\UV4\UV4.exe`，命令行编译与烧录写法
- `stale-paths-in-project-md.md` — 工程 md 里的绝对路径可能是旧电脑的，附本机正确路径对照表
- `context-by-cwd.md` — 按工作目录判断场景，不在 Keil 工程里就别硬把话题引到 STM32
- `ensure-launch-script.md` — 何时创建 `start-claude.bat` 与 `CLAUDE.md`，附调试好的 bat 模板
- `skill-snapshot-frozen.md` — 部署包母本与已安装 skill 的关系、各机器位置、触发名

## 部署到新电脑

```bash
npm install -g @anthropic-ai/claude-code
git clone https://github.com/Feedwyx666/claude-code-stm32-global-config.git
```

把 `CLAUDE.md` 和 `memory/` 放进新机器的 `%USERPROFILE%\.claude\`。`memory/` 是**合并**，不要删掉目标目录里已有的其他记忆。

换机后需要核对的机器特定项：

- `CLAUDE.md` 第 6 条与 `keil-build-flash.md` 里的 Keil 路径，按实际安装位置改
- 桌面若被重定向（本机在 `D:\Users\Y\Desktop`），涉及桌面的判断要跟着改
- 代理与模型配置在 `settings.json`，**不在本仓库内**，需单独迁移

`~/.claude/` 下的路径都写成 `%USERPROFILE%\.claude\`，没有写死用户名，这部分换机不用改。

## 关于 .gitignore

这个仓库的根目录就是 `~/.claude/`，里面大部分内容是 Claude Code 的运行时数据，不该上传：

- **隐私**：`history.jsonl`、`projects/`、`sessions/` 是完整对话历史
- **含凭据**：`settings.json`、`config.json`
- **体积或机器相关**：`cache/`、`skills/`、`plugins/`、`shell-snapshots/`

所以仓库实际只跟踪 13 个文件：`CLAUDE.md`、`.gitignore`、`memory/` 下的 12 个 md。clone 下来不会覆盖你的会话记录。

## 说明

规则是逐次踩坑攒出来的，带着具体的机器环境（Windows + Git Bash + Keil MDK + STM32F103C8T6）。直接拿去用大概需要按自己的环境改一遍，但 `memory/` 里那些「为什么」应该是通用的。
