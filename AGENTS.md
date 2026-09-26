# AGENTS.md

## 项目概览

这是一个用于自动构建 Cisco Meraki MR33 OpenWrt 固件的 GitHub Actions 项目。

目标设备：

- Target：`ipq40xx/generic`
- 架构：`arm_cortex-a7_neon-vfpv4`
- Profile：`meraki_mr33`
- 固件定位：纯 AP / dumb AP
- 语言/界面：LuCI（HTTP）、简体中文
- 无线：`ath10k`，2.4GHz + 5GHz
- 回程：仅有线
- OpenWrt 基线：`24.10.8`

## 固件边界

MR33 只负责：

- 2.4GHz / 5GHz 无线接入
- 二层桥接
- 802.11k/v/r 漫游参数
- 可选 `usteer` 漫游辅助
- LuCI 管理界面（仅内网 HTTP）

MR33 不承担：

- 路由
- NAT
- DHCP
- DNS
- PPPoE
- Mesh
- 802.11s
- EasyMesh
- AC / Fit-AP 控制器
- PassWall 或任何代理功能
- USB 存储

## 版本策略

- 当前固定 OpenWrt `24.10.8`
- 只跟随 OpenWrt 24 stable 家族
- 不自动切到 `25.12` 或更新的大版本
- 出现新的 `24.10.x` 或 `24.11` 系列时，必须先向用户确认，再更新固定版本
- 升级前必须确认 OpenWrt tag、官方 ImageBuilder 和 `ipq40xx/generic/meraki_mr33` profile 存在

## 推荐结构

后续仓库应保持简单：

```text
.github/workflows/build-imagebuilder.yml  # MR33 ImageBuilder 构建流程
files/etc/uci-defaults/                   # 首次启动配置
README.md
LICENSE
```

不要引入：

- PassWall feed
- 源码完整编译 workflow
- 多设备构建矩阵
- 自动跟随分支引用的 GitHub Actions

## 默认配置策略

首次刷入后应保持安全默认：

- Wi-Fi 默认关闭，避免两台 AP 使用相同默认 SSID
- LAN IP 可先使用默认地址，部署时手动固定
- DHCP server 默认关闭
- 无线初始化由用户在 LuCI 中手动完成

## 验证

本仓库没有测试框架。改动后至少执行：

```bash
bash -n files/etc/uci-defaults/*
git diff --check
ruby -e 'require "yaml"; YAML.safe_load_file(".github/workflows/build-imagebuilder.yml", aliases: true)'
```

完整 ImageBuilder 构建耗时较长。除非用户明确要求，不要在本地执行完整构建，优先依赖 GitHub Actions。

## 禁止事项

- 不要把 MR33 构建混入 `gl-mt3000-openwrt` 项目
- 不要把主路由固件混入本仓库
- 不要为了精简而移除无线驱动或无线固件
- 不要默认启用 Wi-Fi
- 不要把路由器侧功能加入 AP 固件
- 不要提交 `imagebuilder/`、下载的压缩包或固件输出
