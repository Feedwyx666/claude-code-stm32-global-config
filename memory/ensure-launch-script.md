---
name: ensure-launch-script
description: 若打开的工作目录没有 claude.bat 启动文件则创建（双击启动 Claude 到该目录）
metadata: 
  node_type: memory
  type: feedback
  originSessionId: bd52e02f-8e9f-4792-aabb-fe91d3c9dab8
  modified: 2026-08-04T18:48:42.482Z
---

如果打开 Claude 的工作目录里**没有启动文件（如 `claude.bat`）**，则**创建**一个，让用户双击即可直接进入 Claude 对话，不必每次手动开终端再输入 `claude`。

**Why:** 用户要求「添加claude.md的同时若当前目录没有启动文件（比如claude.bat）就创建一个，我可以双击打开这个文件，直接与你对话」。
**How to apply:**
- 创建 `claude.bat`，关键点：
  - **必须用 ASCII 纯文本 + CRLF 换行**（Windows 批处理的标准格式），否则 cmd 解析出乱码命令报「不是内部或外部命令」。UTF-8/LF 会导致失败。
  - 用 `cd /d "%~dp0"` 切到脚本所在目录。
  - 用 Windows Terminal 承载 Claude（双击默认 conhost 渲染不了 TUI 会黑屏）：`start "" "%LOCALAPPDATA%\Microsoft\WindowsApps\wt.exe" -- cmd /k claude ...`
  - **`--` 分隔符是必须的**（wt 会把后面的 `/k` 等当成自己的选项）。
  - **不要用 `-d "%~dp0"` 给 wt 传启动目录**：路径末尾的反斜杠+引号会被 Windows 解析成转义引号，导致 `0x8007010b` 无法访问启动目录。用 `cd /d` 就够。
  - **启动文件名绝不能用 `claude.bat`**：会与 `claude` 命令同名，cmd 在当前目录找到同名 bat 又执行 → **无限递归弹窗**拖垮系统。用 `start-claude.bat` 等不含 `claude` 的命名。
- 末尾 `pause` 防止窗口闪退。
- **判断当前目录是否需要创建**（不一律创建）：
  - **需要**：当前目录是**空文件夹/近乎为空**（像是准备从头做工程）→ 创建 `CLAUDE.md` + `start-claude.bat`。
  - **不需要**：目录在**桌面 / C 盘或 D 盘根目录 / 文件很多的普通目录** → 大概率只是单次读取/修改文件，**不要创建**。
  - **检查是否已有**：先 `ls`/实际看目录里是否已有 `start-claude.bat`，已有则跳过（别只信 `CLAUDE.md` 内容）。
- **启动逻辑：双击直接新开会话**（`claude`，不带 `--continue`/`--resume`）。`CLAUDE.md` 会自动加载，新开对话已够用。用户已放弃 continue/resume（`--resume` 的选择器里无可选会话；`--continue` 会续到全局最近而非本目录，且无法选择）。
- **创建 bat 时直接用已调试好的最终模板**（即当前项目 `start-claude.bat` 的内容，见下），不要重新摸索试错：

```bat
@echo off
title Claude Code
set "WT=%LOCALAPPDATA%\Microsoft\WindowsApps\wt.exe"
set "DIR=%~dp0"
set "DIR=%DIR:~0,-1%"
if exist "%WT%" (
    start "" "%WT%" -d "%DIR%" cmd /k claude
) else (
    cd /d "%~dp0"
    claude
    pause
)
```

模板关键点：用 `-d "%DIR%"` 给 wt 传**去尾反斜杠**的目录（保证 claude 落在目标目录）；`%~dp0` 自动适配任意位置；wt 不存在时用 `cd /d` 兜底。
- **会话续接**：`--resume`（不带参数）会打开交互式会话选择器，比 `--continue`（自动续全局最近）更可控，适合不想续错会话的场景（但用户实测选择器无可选会话，已放弃）。
- 该目录若没有 `CLAUDE.md` 也创建一份。
- 参见 [[context-by-cwd]]、[[backup-before-changes]]、[[keil-build-flash]]。
