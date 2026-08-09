---
name: skill-snapshot-frozen
description: 部署 skill 与母本包的关系、各机器位置、触发名与同步纪律（2026-08-08 换机后订正）
metadata:
  node_type: memory
  type: project
---

部署配置包分两个角色：**母本包**（可维护的模板源）和**已安装 skill**（部署到 `~/.claude/skills/` 的副本）。用户在 2026-08-06 晚决定已安装副本**不再随母本单向同步**。

**各机器上的位置：**
- 旧电脑（用户名 `w`）：已安装快照 `C:\Users\w\.claude\skills\setup-claude-config\`，母本 `G:\Claude CLI\claude-config-deploy\`。
- 本机（用户名 `Y`，2026-08-08 部署）：
  - 已安装 skill：`%USERPROFILE%\.claude\skills\claude-config-deploy\`（目录名是 `claude-config-deploy`，不是 `setup-claude-config`）
  - **母本包：`E:\Claude CLI\claude-config-deploy\`**（旧电脑在 G 盘，本机盘符是 E；母本确实在这台机器上）
  - 母本包旁有两份历史备份 `claude-config-deploy_backup_20260806_010516` / `_012431`

**触发名（重要）：**
- skill 按**目录名**触发，不是按 `SKILL.md` frontmatter 的 `name` 字段。
- 本机目录名是 `claude-config-deploy` → 触发命令是 `/claude-config-deploy`。`/setup-claude-config` 在本机**调不出来**。
- 部署到新电脑时，文件夹叫什么名字，命令就是那个名字。

**包内容（2026-08-08 订正后）：**
- `CLAUDE.md` 含 9 条规则（第 9 条为「工程文件夹中先通读工程再回答」）
- `SKILL.md` 为「放置记忆（只合并不删除）」版本
- `memory/` 共 10 个文件（索引 `MEMORY.md` + 9 条记忆，含本文件自身）

**Why:** 用户说「以后不用更新这个 skill 包了，但保留今晚的更新作为一个快照」。已安装 skill 的本质是部署副本，不是需要持续从母本拉取的模板。

**How to apply:**
- 冻结的含义是「不再从母本单向同步」，**不等于禁止修改**。为适配本机而做的必要修正（路径、Keil 位置、事实订正）照常改。
- 本机改动要覆盖**三处**，否则重新部署会用旧内容覆盖回去：
  1. 已部署配置 `%USERPROFILE%\.claude\CLAUDE.md` + `%USERPROFILE%\.claude\memory\`
  2. 已安装 skill `%USERPROFILE%\.claude\skills\claude-config-deploy\`
  3. 母本包 `E:\Claude CLI\claude-config-deploy\`
- 母本包旁的 `新电脑部署清单.txt`（`E:\Claude CLI\`）是给新电脑用的分步说明，改了包内容也要同步它。
- 相关：[[read-project-before-answer]]、[[ensure-launch-script]]、[[keil-build-flash]]。
