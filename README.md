# Intel NUC9 i9-9980HK macOS Hackintosh

Intel NUC9 Ghost Canyon（NUC9i9QNX，i9-9980HK）黑苹果 EFI，基于 OpenCore。

**已实测：macOS Sequoia 15.x 与 macOS Tahoe 26.6.1 均安装完成并稳定运行。**

完整说明见 [NUC9/README.md](./NUC9/README.md)。

## 仓库结构

- `NUC9/` — 本机（i9-9980HK）工作 EFI，包含完整 OpenCore 配置
- `EFI-macOS-Sonoma-14.3-OpenCore/` — 参考项目（i7-9850H）的 Sonoma EFI，保留备份
- `docs/` — 参考项目文档

## 快速开始

1. 将 `NUC9/EFI` 放入 EFI 分区
2. 用 GenSMBIOS 重新生成 **MacBookPro16,4** 序列号（仓库内为占位符）
3. BIOS：关闭 Secure Boot / Fast Boot / CSM；SATA=AHCI；IGD Minimum Memory=64MB；Aperture=256MB
4. OpenCore 选择 macOS 启动

## 关键修复点（踩坑记录）

- `SecureBootModel = Disabled` — 否则安装器 restore 阶段会"优雅重启"循环
- `DevirtualiseMmio = false` — 避免早期内核 `_vc_display_lzss_icon` panic
- iGPU 帧缓冲：`07009B3E` + `framebuffer-patch-enable` + `stolenmem` + `fbmem`
- macOS Tahoe：OpenCore 1.0.7 + RestrictEvents + `-ibtcompatbeta revpatch=sbvmm`
- **蓝牙修复（Tahoe）**：IntelBTPatcher `maxKernel=Sequoia` 在 Tahoe 下不激活 → 升级 IBTF/IntelBTPatcher 到 **2.5.1**（lshbluesky 社区版）+ BlueToolFixup **2.7.2**，并在 NVRAM 写入 `bluetoothExternalDongleFailed=00` 与 `bluetoothInternalControllerInfo`（14 字节 0x00），否则 bluetoothd 禁用蓝牙（详见 `NUC9/README.md`）
- **USBMap 在 macOS 26 下保持禁用**——26 的 `AppleUSBHostMergeProperties` 合并会破坏端口；原生 XHCI 已枚举全部端口（11.3+ 无 15 端口限制）
- ⚠️ macOS 26 移除 AppleHDA，**模拟音频（AppleALC）与纯 iGPU 的 HDMI/DP 音频均失效**（iGPU 显示音频依赖被删的 cAVS HDA 控制器，AppleGFXHDA 无控制器可挂）；**USB 音频正常**，dGPU 显示音频正常（详见 `NUC9/README.md`）

## 支持

- macOS Sequoia 15.x ✅
- macOS Tahoe 26.6.1 ✅
- 有线网卡：IntelMausi + AppleIntelI210Ethernet（`e1000=0`）✅
- 无线：Intel AX200（itlwm 2.3.0 + HeliPort 1.5）✅
- 蓝牙：Intel（IBTF 2.5.1 + IntelBTPatcher 2.5.1 + BlueToolFixup 2.7.2 + NVRAM 键）✅
- SD 读卡器：插卡正常识别 ✅
- 睡眠：已禁用（`pmset -a sleep 0`，避免远程断连）✅

## 致谢

参考项目（i7-9850H 版）：https://github.com/0xHJK/Intel-NUC9-i7-9850H-macOS
OpenCore 官方指南：https://dortania.github.io/OpenCore-Install-Guide/
