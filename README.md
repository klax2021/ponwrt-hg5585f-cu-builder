# PonWrt HG5585F 联通版固件自动构建

私有仓库。只存放本构建工作流，不 Fork 上游。每次触发时动态拉取
[wuziwei/ponwrt-ZN504-HG5585F](https://github.com/wuziwei/ponwrt-ZN504-HG5585F) 最新 master，
完全复刻上游编译流程，仅收集 `fiberhome_hg5585f-cu`（烽火 HG5585F 联通版）产物。
**不发布 Release**：产物仅上传本仓库 Actions Artifact（保留 90 天）并推送到 Telegram。

## 固件内容

- 机型：烽火 HG5585F 联通版 `fiberhome_hg5585f-cu`
- 内核底座：eBPF CO-RE + BTF、`kmod-sched-bpf/core`、`kmod-veth`、`kmod-nf-tproxy`、931 IPv6 PPE 硬件卸载修复
- 包策略：完全跟随上游（Honk / Tailscale / rtp2httpd 等一律保留）
- sing-box：**不内置**。固件只提供运行 MiChong eBPF 内核所需的内核底座

## 刷机后启用 MiChong sing-box

从 [MiChongs/sing-box releases](https://github.com/MiChongs/sing-box/releases) 下载
`sing-box-linux-arm64-ebpf`（基于 SagerNet 1.15.0-alpha.13，含 eBPF 入站），
上传到路由器 `/usr/bin/sing-box`，执行 `chmod +x /usr/bin/sing-box`，再配好配置启动即可。
放 `/usr/bin` 而非 `/tmp`，避免重启丢失。

## 触发构建

- 自动：每周一 23:17（北京时间）检查上游 README 的 Changelog 版本，
  - 有新版本 → 立即编译 → 编译完成后把「更新日志 + 固件 itb」一起推送到 Telegram，并上传 Actions Artifact
  - 无新版本 → 只发一条简短提示，不编译
- 手动：Actions → Weekly Check & Build HG5585F-CU → Run workflow
  - `force_build` 默认勾选（强制编译，忽略有无更新）；`clean_ccache` 按需勾选

编译约 1.5~2.5 小时。更新日志来源为上游 README 的「最新版本核心更新动态 (Changelog)」章节，
日志基线在每次编译成功后自动更新（记录在 `build-base-release.txt`）。

## Telegram 固件附件说明

Telegram Bot 单文件上限 50MB。推送逻辑：固件 ≤48MB 直接发送；超过则先 zip 压缩，
仍超则自动拆为 45MB 分卷并在 Telegram 给出合并命令（`cat fw.zip.part* > fw.zip && unzip fw.zip`）。
