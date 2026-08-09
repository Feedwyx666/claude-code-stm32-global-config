---
name: keil-build-flash
description: 本机 Keil 编译与烧录 STM32 项目的命令行方法
metadata:
  type: reference
---

本机 STM32 Keil 项目的命令行编译与烧录方法。

**Keil 位置**：`C:\Keil_v5\UV4\UV4.exe`（本机在 C 盘根目录；旧电脑曾在 `D:\Keil_v5`，换机后已改）。

**编译**（在项目目录下）：
```
"/c/Keil_v5/UV4/UV4.exe" -b "Project.uvprojx" -j0 -o "build_log.txt"
```
- `-b` 构建；`-j0` 并行编译（注意：`-j0` 时会用 V5.06 ARMCC 而非项目配置的 uAC6，但产物可正常烧录；界面 Rebuild 才用 uAC6）。
- 成功标志：`0 Error(s), 0 Warning(s)`，生成 `Objects\Project.axf`。

**烧录**（项目配的是 CMSIS-DAP 调试器）：
```
"/c/Keil_v5/UV4/UV4.exe" -f "Project.uvprojx" -o "flash_log.txt"
```
- `-f` 会按项目配置的烧录算法（`STM32F10x_128.FLM`）通过 CMSIS-DAP 下载。
- 成功标志：`Erase Done. Programming Done. Verify OK. Application running ...`

**注意事项**：
- 项目类型：STM32F103C8（Cortex-M3），C 文件用 `u16`/`u8` 等库类型，需包含 stm32f10x 库。
- 若改的是中断处理，注意 EXTI 重新使能要直接操作 `EXTI->IMR`，不要用 `EXTI_Init()` 改边沿触发。
- 参见 [[backup-before-changes]]。
