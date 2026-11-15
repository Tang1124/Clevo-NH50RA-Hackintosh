# Clevo NH50RA / i5-9300H UHD630 Mackintosh MacOS26.X OpenCore EFI

蓝天 NH50RA 准系统（i5-9300H UHD630）的黑苹果 OpenCore 配置，当前在 **macOS 26.7.2（Tahoe）** 上日常使用：内屏、背光、HDMI 外接、声卡、有线网卡、睡眠唤醒全部正常。

> **使用前必读三条**
> 1. 请自行生成三码（第 5 节），仓库里是随机占位值。
> 2. **Intel 无线与蓝牙驱动默认已关闭**（第 6 节）。
> 3. 本 EFI 是按 **macOS 26 (Tahoe)** 设计和验证的，其他系统版本请看第 2 节，不要盲目套用。
> 4. 安装过程请保持联网，安装完成后请断网进入系统修改三码（如提前修改完三码请忽略）

---

## 1. 兼容机型

| 机型 | 状态 |
| --- | --- |
| Clevo NH50RA | ✅ 完整验证 |
| 神舟 Z7M-CT5NA（同模具） | ✅ 实测可用 |
| **Clevo NH5xRD / NH5xRC / NH5xRA / NH5xRH(Q) 系列全部机型** | ⚠️ 理论支持 自行测试 |
| **Clevo NH70RD / NH70RC / NH70RA / NH70RH(Q) 系列全部机型** | ⚠️ 理论支持 自行测试 |
| 神舟 Z7 / Z7M 系列等采用同 PCB 的其他品牌机型 | ⚠️ 理论可用 自行测试 |

## 2. macOS 版本适用范围

| 系统版本 | 适用性 | 说明 |
| --- | --- | --- |
| **macOS 26.0 – 26.x（Tahoe）** | ✅ **已验证（26.7.2）** | 目前一切正常 |
| macOS 15.x（Sequoia） | ⚠️ 未验证，需改造 | `SimpleHDA` 与 `AirportItlwm` 因 MinKernel 限制不会加载：声卡要退回“仅 AppleALC + 系统自带 AppleHDA”，无线要换成对应 15.x 版本的 AirportItlwm |
| macOS 14.x（Sonoma）及更早 | ❌ 不建议直接套用 |  |

## 3. 硬件与系统

| 项目 | 型号 |
| --- | --- |
| 准系统 | Clevo NH50RA |
| CPU | Intel Core i5-9300H |
| 核显 | Intel UHD Graphics 630 |
| 独显 | NVIDIA GeForce **GTX 1650**，已屏蔽 |
| 声卡 | Realtek ALC269VC（AppleALC layout-id **269**，定制已提交上游，见第 8 节） |
| 有线网卡 | Realtek RTL8111 |
| 无线网卡 | Intel AX210（驱动默认关闭，见第 6 节） |
| 硬盘 | NVMe |
| SMBIOS | MacBookPro16,1 |
| 系统 | macOS 26.7.2（Tahoe） |
| 引导 | OpenCore  |

## 4. 工作状态

| 硬件 / 功能 | 状态 | 实际情况 |
| --- | --- | --- |
| 核显 UHD630 | ✅ 完美 | Metal 3 硬件加速正常，动态显存 1536MB |
| 独显 GTX 1650 | ⛔ 已屏蔽 | **作者未测试「1401」版本 NVIDIA 显卡驱动，如需让独显参与输出请自行测试** |
| 内屏 1920×1080 | ✅ 完美 | 背光可调、Fn 亮度快捷键有效、合盖正常 |
| HDMI 外接 | ✅ 可用 | 最高 2560×1440@60（受 HDMI 1.4 / TMDS 带宽限制） |
| HDMI 音频 | ❌ 不可用 | 见第 9 节，硬件限制，非配置问题 |
| **睡眠 / 唤醒** | ✅ **完美** | 合盖睡眠、手动睡眠、唤醒全部正常 |
| 声卡 ALC269VC | ✅ 完美 | `layout-id 269`：内建扬声器（2 声道 / 44.1kHz）与内建麦克风均正常，AppleALC 定制已提交上游 |
| 有线网卡 RTL8111 | ✅ 完美 | 千兆速率协商正常 |
| 电池 | ✅ 完美 | 电量百分比、充电状态、循环计数正常 |
| 触摸板 / 键盘 | ✅ 完美 | I2C 触摸板多指手势，PS/2 键盘与 Fn 键正常 |
| SD 读卡器 | ✅ 可用 |  |
| Wi-Fi / 蓝牙 | ⛔ 默认关闭 | 见第 6 节，换卡后按说明启用 |

## 5. 三码：必须自己生成

本仓库三码为随机占位，**不要直接登录 Apple ID**。

## 6. 无线网卡：默认关闭，换卡后按需开

原机型的无线网卡不是 AX210，所以仓库里 **AirportItlwm / IntelBluetoothFirmware / IntelBTPatcher / BlueToolFixup 全部设为 Enabled = false**。kext 文件仍保留在 `EFI/OC/Kexts`，只是不加载。

## 7. HDMI 

**换显示器怎么办**：注入你自己的EDID

## 8. 驱动清单（Kext / 版本 / 上游仓库）

按加载顺序排列，共 24 个启用的 kext 条目（含子插件）。

| Kext | 版本 | 上游仓库 | 作用 |
| --- | --- | --- | --- |
| Lilu | 1.7.3 | https://github.com/acidanthera/Lilu | 内核补丁框架，所有插件的基础 |
| VirtualSMC | 1.3.8 | https://github.com/acidanthera/VirtualSMC | 模拟 SMC，必需 |
| SMCBatteryManager | 1.3.8 | https://github.com/acidanthera/VirtualSMC | 电池电量与充电状态 |
| SMCLightSensor | 1.3.8 | https://github.com/acidanthera/VirtualSMC | 环境光传感器 |
| SMCProcessor | 1.3.8 | https://github.com/acidanthera/VirtualSMC | CPU 温度/频率监控 |
| SMCSuperIO | 1.3.8 | https://github.com/acidanthera/VirtualSMC | 风扇转速与主板传感器 |
| WhateverGreen | 1.7.1 | https://github.com/acidanthera/WhateverGreen | 核显 framebuffer 补丁、EDID 注入、AGDC 停用 |
| AppleALC | 1.9.8 | https://github.com/acidanthera/AppleALC | 声卡 layout 注入。**本机 ALC269VC 的 layout-id 269 已提交到 AppleALC 官方仓库（PR #982），预计随下个版本合并**；合并后可直接用官方版本，不必依赖本仓库的定制 kext |
| SimpleHDA | 1.0.1 | https://github.com/laobamac/SimpleHDA | Tahoe 下注入原版 AppleHDA（配合上一条使用）。依赖 `Kernel → Quirks → DisableLinkeditJettison = true`（本 config 已开启） |
| CtlnaAHCIPort | 341.0.2 | https://github.com/dortania/OpenCore-Install-Guide | 补回被 Apple 移除的 SATA/AHCI 控制器支持 |
| AMFIPass | 1.4.1 | https://github.com/dortania/OpenCore-Legacy-Patcher | 免开机参数放行 AMFI，用于各类注入 |
| RealtekRTL8111 | 2.4.2 | https://github.com/Mieze/RTL8111_driver_for_OS_X | 有线网卡 |
| IOSkywalkFamily | 1.0 | https://github.com/dortania/OpenCore-Legacy-Patcher | 博通 Wi-Fi 方案组件（MinKernel 24.x，Tahoe 不加载） |
| IO80211FamilyLegacy | 1200.12.2b1 | https://github.com/dortania/OpenCore-Legacy-Patcher | 同上 |
| AirportItlwm | 2.4.0 | https://github.com/OpenIntelWireless/itlwm | Intel 无线（MinKernel 25.0.0）。**默认关闭** |
| BlueToolFixup | 2.7.2 | https://github.com/acidanthera/BrcmPatchRAM | 修复蓝牙在较新系统上的初始化。**默认关闭** |
| IntelBluetoothFirmware | 2.5.0 | https://github.com/OpenIntelWireless/IntelBluetoothFirmware | Intel 蓝牙固件加载。**默认关闭** |
| IntelBTPatcher | 2.5.0 | https://github.com/OpenIntelWireless/IntelBluetoothFirmware | Intel 蓝牙补丁。**默认关闭** |
| VoodooPS2Controller | 2.3.8 | https://github.com/acidanthera/VoodooPS2 | PS/2 键盘与触摸板（含 VoodooPS2Keyboard 子插件） |
| BrightnessKeys | 1.0.3 | https://github.com/acidanthera/BrightnessKeys | Fn 亮度快捷键 |
| VoodooI2C | 2.9.1 | https://github.com/VoodooI2C/VoodooI2C | I2C 触摸板/触摸屏（含 VoodooInput、VoodooI2CServices、VoodooGPIO 子插件） |
| VoodooI2CHID | 1（VoodooI2C 子插件） | https://github.com/VoodooI2C/VoodooI2C | I2C HID 设备支持 |
| RealtekCardReader | 0.9.7 | https://github.com/0xFireWolf/RealtekCardReader | Realtek SD 读卡器 |
| RealtekCardReaderFriend | 1.0.4 | https://github.com/0xFireWolf/RealtekCardReaderFriend | 让读卡器在系统信息里显示为内建 |

## 9. 已知限制

- **HDMI 音频不可用**
- **独显 GTX 1650 未做任何驱动尝试**
- 热重启时 OpenCore 选单键盘可能失灵，建议**关机再开机**。

## 10. 版本信息

| 项目 | 内容 |
| --- | --- |
| 引导 | **OpenCore 1.0.8**，运行时版本串 `REL-108-2026-09-27`（即 release 构建，构建日期 **2026-09-27**） |
| EFI 配置文件 | `config.plist`（XML plist，2026-11-16） |
| 目标系统 | macOS 26.7.2（Tahoe，26.x） |
| 引导驱动 | `OpenRuntime.efi`、`OpenCanopy.efi`（图形选单）、`apfs_aligned.efi`、`HfsPlus.efi`、`ResetNvramEntry.efi` |
| 调试开关 | `Misc → Debug` 全部关闭，`Target = 0`，boot-args 无任何调试项 |

## 11. 使用步骤

1. 把 `EFI` 文件夹整体放入 EFI 分区根目录（FAT32）。
2. BIOS 中关闭 Secure Boot、开启 AHCI。
3. 首次启动前先按第 5 节改三码。
4. 若你的无线网卡是 Intel AX210 等，按第 6 节启用对应 kext。
5. 首次启动建议加 `-v` 观察，进入系统后移除。

## 12. 免责与致谢

感谢 Apple、Acidanthera、OpenIntelWireless、dortania、VoodooI2C、0xFireWolf、laobamac 等项目的开发者。

黑苹果属非官方支持的使用方式，本配置仅作同型号机器的参考起点。不同批次的硬件可能有差异，请自行核对后使用，风险自负。
