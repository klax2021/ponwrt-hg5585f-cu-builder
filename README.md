# PonWrt HG5585F 联通版固件自动构建

私有仓库。只存放本构建工作流，不 Fork 上游。每次触发时动态拉取
[wuziwei/ponwrt-ZN504-HG5585F](https://github.com/wuziwei/ponwrt-ZN504-HG5585F) 最新 master，
完全复刻上游编译流程，仅收集 `fiberhome_hg5585f-cu`（烽火 HG5585F 联通版）产物并发布到本仓库 Release。

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

- 自动：每周一 23:17（北京时间）检测上游更新并编译
- 手动：Actions → Build PonWrt HG5585F-CU → Run workflow（内核大改时勾选清空 Ccache）

构建约 1.5~2.5 小时，完成后在本仓库 Releases 下载 `*hg5585f-cu*sysupgrade.itb`。
