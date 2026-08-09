---
name: stale-paths-in-project-md
description: 旧电脑带过来的工程 CLAUDE.md 里的绝对路径可能是错的，以本机实际路径为准，不要照抄工程 md
metadata:
  type: reference
---

从旧电脑复制过来的工程文件夹，里面的 `CLAUDE.md`（以及 `start-claude.bat`、笔记、脚本）可能写着**旧电脑的绝对路径**。这些路径在本机是错的，**不要照抄执行**。

**Why:** 2026-08-08 换机后核查发现，`E:\Claude CLI\exit外部中断\CLAUDE.md` 第 35、37 行仍写着 `"/d/Keil_v5/UV4/UV4.exe"` 的编译/烧录命令，而本机 Keil 实际在 C 盘。工程 md 是当时在旧电脑上写的，跟着文件夹一起复制过来，内容没随机器更新。同理，旧电脑用户名是 `w`、配置母本在 `G:` 盘，本机分别是 `Y` 和 `E:` 盘。

**本机实际路径（以此为准）：**

| 用途 | 本机正确路径 | 旧电脑（已失效） |
|------|------------|----------------|
| Keil UV4 | `C:\Keil_v5\UV4\UV4.exe`（Git Bash 写 `/c/Keil_v5/UV4/UV4.exe`） | `D:\Keil_v5\...` |
| 用户目录 | `C:\Users\Y\`（推荐写 `%USERPROFILE%`） | `C:\Users\w\` |
| 配置母本 | `E:\Claude CLI\claude-config-deploy\` | `G:\Claude CLI\...` |
| 桌面 | `D:\Users\Y\Desktop`（**已重定向，不在 C 盘**） | — |

**How to apply:**
- **优先级：全局记忆（本机事实）> 工程 `CLAUDE.md`**。两者的绝对路径冲突时，用全局记忆这份，工程 md 视为过期信息。
- 在工程目录里要编译/烧录前，**先确认 exe 是否真的存在**（如 `ls "/c/Keil_v5/UV4/UV4.exe"`），不要直接跑工程 md 里抄来的命令然后报错。
- 命令失败且报「找不到文件/不是内部或外部命令」时，**先怀疑是旧路径**，再查其他原因。
- 只在用户明确要求时才去改工程 md 里的路径；否则按正确路径执行即可，不擅自改工程文件（工程 md 属于用户的项目内容）。
- 已知受影响：`E:\Claude CLI\exit外部中断\CLAUDE.md`（第 35、37 行）。`OLED\`、`TIM定时中断\` 的 CLAUDE.md 已查，未含过期绝对路径。
- 判断路径是否可疑的信号：出现 `C:\Users\w`、`D:\Keil_v5`、`G:\` 这三类字样。
- 参见 [[keil-build-flash]]（本机 Keil 命令）、[[read-project-before-answer]]（工程内先通读）。
