# Intel NUC9 i9-9980HK macOS Hackintosh

Intel NUC9 Ghost Canyon（NUC9i9QNX，i9-9980HK）黑苹果 EFI，基于 OpenCore。

**已实测：macOS Sequoia 安装完成并稳定运行；已做好 macOS Tahoe 升级前置。**

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
- macOS Tahoe 前置：OpenCore 1.0.7 + RestrictEvents + `-ibtcompatbeta revpatch=sbvmm`
- ⚠️ macOS 26 移除 AppleHDA，模拟音频（AppleALC）失效

## 支持

- macOS Sequoia 15.x ✅
- macOS Tahoe 26（前置就绪）🚧
- 无线/蓝牙：Intel AX200 + itlwm/HeliPort；蓝牙 Intel 原生 kext

## 致谢

参考项目（i7-9850H 版）：https://github.com/0xHJK/Intel-NUC9-i7-9850H-macOS
OpenCore 官方指南：https://dortania.github.io/OpenCore-Install-Guide/
