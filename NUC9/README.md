# Intel NUC9 Ghost Canyon (i9-9980HK) Hackintosh

基于 OpenCore 的 Intel NUC9 幽灵峡谷黑苹果 EFI，型号 **NUC9i9QNX（i9-9980HK）**。

本 EFI 已实测完成 macOS Sequoia 的安装并稳定运行，正在准备升级 macOS Tahoe。

## 硬件配置

| 部件 | 型号 |
| :--- | :--- |
| 主板 | Intel NUC9 Extreme（Ghost Canyon，CM246 芯片组） |
| CPU | Intel Core i9-9980HK（8C16T） |
| 核显 | Intel UHD Graphics 630 |
| 内存 | 64GB DDR4 |
| 存储 | 1x NVMe + 3x SATA |
| 有线网卡 | Intel I219-LM + I210-AT（IntelMausi + AppleIntelI210Ethernet） |
| 无线网卡 | Intel Wi-Fi 6 AX200（可选，itlwm/HeliPort） |
| 蓝牙 | Intel（IntelBluetoothFirmware + BlueToolFixup + IntelBTPatcher） |

## 支持状态

- ✅ macOS Sequoia 15.x：安装完成、稳定运行
- ✅ macOS Tahoe 26.6.1：升级完成、稳定运行
- ✅ 有线网卡：IntelMausi + AppleIntelI210Ethernet（`e1000=0`）
- ✅ 蓝牙：Intel（IntelBluetoothFirmware + BlueToolFixup + IntelBTPatcher，`-ibtcompatbeta`）
- ⚠️ 无线：itlwm 2.3.0 需要 HeliPort；Tahoe 需 OpenIntelWireless nightly
- ⚠️ 模拟音频：macOS 26 移除 AppleHDA，AppleALC 失效（HDMI/DP/USB 数字音频不受影响）
- ⚠️ USBMap：macOS 26 下禁用（26 的合并机制会破坏端口；原生 XHCI 枚举全部端口）
- ❌ Airdrop / Handoff：Intel 无线不支持

## 关键配置

- OpenCore 1.0.7（RELEASE）
- SMBIOS：MacBookPro16,4（i9-9980HK 同款 CPU，macOS Tahoe 官方支持）
- `SecureBootModel = Disabled`（关键：安装器 restore 阶段需要，否则会优雅重启循环）
- `DevirtualiseMmio = false`（本机固件下避免早期内核 panic）
- iGPU 帧缓冲：`07009B3E` + `framebuffer-patch-enable` + `framebuffer-stolenmem` + `framebuffer-fbmem`
- USBMap：macOS 26 下已禁用（原生 XHCI 枚举全部端口，26 的合并机制会破坏端口）

### Kexts

Lilu / VirtualSMC / WhateverGreen / RestrictEvents / IntelMausi / AppleIntelI210Ethernet / USBMap / SMCProcessor / SMCSuperIO / IntelBluetoothFirmware / BlueToolFixup / IntelBTPatcher / itlwm

## BIOS 设置

- 关闭：Secure Boot、Fast Boot、CSM/Legacy
- SATA Mode：AHCI
- IGD Minimum Memory：64MB
- IGD Aperture Size：256MB
- 开启：VT-x、Above 4G Decoding、Hyper-Threading

## 使用

1. 将本 `EFI` 目录放入硬盘/优盘的 EFI 分区
2. 用 GenSMBIOS 重新生成 **MacBookPro16,4** 的序列号/MLB/UUID（本仓库为占位符，请勿直接使用）
3. OpenCore 选择器中选择 macOS 启动

## 参考资料与致谢

- 参考项目：https://github.com/0xHJK/Intel-NUC9-i7-9850H-macOS
- OpenCore 官方指南：https://dortania.github.io/OpenCore-Install-Guide/
