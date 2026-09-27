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
- 加入精简的普通 HTTP LuCI 组件、简体中文、`wpad-basic-mbedtls` 和可选 `usteer`；
- 首次启动将 LAN 设置为 DHCP client；
- 固件不包含 DHCP/DNS server 和 Firewall 服务；`usteer` 保留软件但默认关闭；所有 Wi-Fi radio 首次启动关闭；
- 不预设 SSID、密码、信道或未知 radio 的特殊行为；
- 移除可独立移除的 DNS、DHCP server 和 USB 相关组件；
- 由于这是仅在内网访问的管理 AP，固件不启用 LuCI HTTPS，避免引入 TLS 后端；
  LuCI 不使用 `luci-light` 组合包，而是显式选择管理页面所需组件，删除 Firewall、PPP、IPv6 LuCI 协议和 NFT 相关包；
  `usteer` 软件保留，但通过 ImageBuilder 的 `DISABLED_SERVICES` 默认关闭。

当前软件构建阶段已完成，最新构建已发布并通过 CI 校验：

- Release：`MR33_AP_24.10.8_36260615447_1`
- 固件：`openwrt-24.10.8-ipq40xx-generic-meraki_mr33-squashfs-sysupgrade.bin`
- 固件大小：`8,356,912` bytes（约 `7.97 MiB`）
- SHA256：`b8900b6e7c343e819c35b141448f8a16d26840a48b93b2a2782a82c5adb623d7`

当前下一步是实机验证，而不是继续盲目删包。刷入 MR33 后需要确认：

```sh
wifi status
iw dev
dmesg | grep ath10k
```

同时确认 LAN 能从 MT3000 获取管理地址、LuCI HTTP 可以访问、首次启动 Wi-Fi 保持关闭、`usteer` 默认未运行，并根据实际 radio 枚举结果决定是否需要调整无线配置。未完成实机输出确认前，不预设 SSID、密码、信道、802.11k/v/r 或第三 radio 行为。

## License

MIT
