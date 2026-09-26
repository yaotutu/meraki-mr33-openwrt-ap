# Meraki MR33 OpenWrt AP Firmware

这是一个独立维护的 Cisco Meraki **MR33** OpenWrt 纯 AP 固件构建项目。

项目定位：

- 设备：Cisco Meraki MR33
- 角色：吸顶哑 AP / dumb AP
- OpenWrt：`24.10.8`
- 回程：仅有线
- 不承担路由、NAT、DHCP、DNS、Mesh、AC 或代理功能
- 保留 2.4GHz / 5GHz Wi-Fi、`ath10k`、`hostapd/wpad`、LuCI（HTTP）和可选漫游辅助能力

## 目标架构

```text
Internet
  |
主路由（GL-MT3000）
  |
PoE 交换机
  |-- 吸顶 MR33 #1
  `-- 吸顶 MR33 #2
```

## 维护原则

- 只跟随 OpenWrt 24 stable 家族
- 使用官方 ImageBuilder 构建
- AP 只做二层无线接入
- 固件必须保持轻量，禁止加入路由器侧功能
- 修改后至少执行脚本语法检查、YAML 解析和 `git diff --check`
- 完整固件构建优先依赖 GitHub Actions 验证

## 状态

官方基线已经完成构建验证。当前进入第一版 AP 定制：

- 继续使用 OpenWrt `24.10.8` 官方 ImageBuilder；
- 使用 `meraki_mr33` 官方 profile；
- 加入普通 HTTP LuCI、简体中文、`wpad-basic-mbedtls` 和可选 `usteer`；
- 首次启动将 LAN 设置为 DHCP client；
- 首次启动关闭 DHCP/RA 服务、Firewall、`usteer`、所有 Wi-Fi radio；
- 不预设 SSID、密码、信道或未知 radio 的特殊行为；
- 移除可独立移除的 DNS、DHCP server 和 USB 相关组件；
- 由于这是仅在内网访问的管理 AP，固件不启用 LuCI HTTPS，避免额外引入 TLS 后端；
  LuCI 仍会带入部分 Firewall/PPP/NFT 依赖，但这些包只作为界面依赖保留，构建时通过 ImageBuilder 的 `DISABLED_SERVICES` 禁用 Firewall 和 `usteer`，不配置 PPPoE、NAT 或防火墙规则。

该版本仍需在真实 MR33 上验证 `wifi status`、`iw dev` 和 `dmesg | grep ath10k`，之后再决定是否调整 radio 或漫游配置。

## License

MIT
