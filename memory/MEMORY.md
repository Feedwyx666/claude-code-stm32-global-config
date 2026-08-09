# 全局记忆索引

- [用中文](use-chinese.md) — 所有回复使用中文
- [改动前留回退点](backup-before-changes.md) — 有 git 仓库用 git 提交（+推送远端）代替备份；无 git 才做文件复制备份
- [Keil 编译与烧录](keil-build-flash.md) — 本机 Keil 命令行编译/烧录 STM32 项目的方法
- [学习 STM32 开发](user-learning-stm32.md) — 用户目的：学习 STM32 嵌入式开发，注重原理讲解
- [询问语气先确认](confirm-before-edits.md) — 用户用询问语气提问时先解释并确认，不马上改代码
- [按工作目录判断用途](context-by-cwd.md) — 根据打开终端位置判断使用目的，不硬引到 STM32
- [CLAUDE.md 启动文件](ensure-launch-script.md) — 若工作目录没有 CLAUDE.md 则添加一个
- [先通读工程再回答](read-project-before-answer.md) — 仅工程文件夹：回答前先完整通读用户编辑过的工程文件，不凭直觉
- [部署包与已安装 skill 的关系](skill-snapshot-frozen.md) — 母本在 `E:\Claude CLI\claude-config-deploy\`，已安装 skill 目录名 `claude-config-deploy`，触发命令 `/claude-config-deploy`
- [工程 md 里的过期绝对路径](stale-paths-in-project-md.md) — 旧电脑带来的工程 CLAUDE.md 里绝对路径可能是错的（如 `D:\Keil_v5`），以全局记忆的本机事实为准
- [不要往桌面放文件](desktop-file-placement.md) — 桌面开了自动排列，新建文件/文件夹会挤掉用户图标位置；默认放别处，必须放桌面先问
