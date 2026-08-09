---
name: backup-before-changes
description: 改动前的保护策略——有 git 仓库则用 git 提交（并推送远端）代替备份；无 git 才做文件复制备份
metadata: 
  node_type: memory
  type: feedback
  originSessionId: bd52e02f-8e9f-4792-aabb-fe91d3c9dab8
  modified: 2026-08-10T02:10:00.000Z
---

改动文件前必须先留一个**可回退点**。**第一步永远是判断当前目录有没有 git 版本控制**，再决定用哪种方式。

**Why:** 用户最初要求「不用每次都保存备份，只需要在首次在这个项目文件夹内使用 claude 时进行备份」「若当前目录不是什么工程目录，则不用备份」「在桌面或C/D盘根目录或文件很多时，只备份你要修改的文件」。2026-08-10 用户把 `E:\Claude CLI\pwm驱动led呼吸灯` 纳入 git 并推送 GitHub 后，明确要求：「存在 git 版本控制时，把备份操作改为 git 提交，若有对应的远程仓库也一并提交，后再做修改」。git 提交本身就是精确、带历史、可 `git diff` / `git checkout` 回退的快照，比复制整个目录更省空间也更好用，同时存在两套机制反而冗余。

**How to apply:**

**第一步：判断有无 git 仓库**

```bash
git rev-parse --git-dir 2>/dev/null    # 成功 → 在 git 仓库内
```

**情况 A：在 git 仓库内 → 用 git 提交代替备份，不做任何文件复制**

1. `git status --short` 看工作区状态。
2. **有未提交改动** → 先把现状存档，作为改动前的回退点：
   ```bash
   git add <相关文件>          # 逐个列出，不用 git add .
   git commit -m "改动前存档：<简述当前状态>"
   ```
3. **有远程仓库** → 一并推送，让回退点也存在远端：
   ```bash
   git remote -v               # 非空则有远端
   git push
   ```
4. **工作区本来就干净** → 无需提交，当前 HEAD 就是回退点，直接开始改。
5. 改完在回复中告知用户：已提交（附 commit 简述）、是否已推送远端。

**情况 B：不在 git 仓库内 → 按目录类型做文件复制备份**

- **工程目录**（含 `.uvprojx`/`.sln`/`package.json`/`CMakeLists.txt` 等工程文件）：首次在本项目文件夹使用 Claude 时，在项目**上一级目录**创建 `<项目名>_backup_YYYYMMDD_HHMMSS\` 完整备份；已有则跳过。之后不再每次备份。
- **普通/文件多目录**（桌面、C 盘/D 盘根目录、Downloads，含**可能为空的工程文件夹**）：**不要备份整个目录**，**只备份你要修改或操作的单个文件**（复制一份带时间戳后缀，或放进 `_file_backups` 子目录）。
- **纯临时/测试目录**：不备份。
- **所有备份最多保留两份，新的自动覆盖最旧的**（超出 2 份就删除最旧一份）。
- 备份不要放进 `%USERPROFILE%\.claude\backups\`（除非是单文件备份）。
- 备份后立刻核对文件已生成、内容与源文件一致，并在回复中一句话告知用户。

**注意事项：**
- 用户明确说「不用备份」时以用户为准，不要坚持（如 2026-08-10 仓库化操作时用户说「这些步骤不要做备份了没有意义」）。
- 推送失败（无网络、无权限、远端有冲突）不要硬推，也不要用 `--force`；本地提交已经是有效回退点，告知用户推送失败原因即可。
- 情况 A 下**不要**再额外复制目录，两套机制并存是冗余。
- 已知已纳入 git 的目录：`E:\Claude CLI\pwm驱动led呼吸灯`（远端 `Feedwyx666/LED-Servo-OLED-Encoder-work-with-Stm32f103c8t6`）。
- 参见 [[use-chinese]]、[[keil-build-flash]]、[[confirm-before-edits]]。
