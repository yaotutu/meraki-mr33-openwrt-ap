# Meraki MR33 OpenWrt AP Firmware

这是一个独立维护的 Cisco Meraki **MR33** OpenWrt 纯 AP 固件构建项目。

项目定位：

- 设备：Cisco Meraki MR33
- 角色：吸顶哑 AP / dumb AP
- OpenWrt：`24.10.8`
- 回程：仅有线
- 不承担路由、NAT、DHCP、DNS、Mesh、AC 或代理功能
- 保留 2.4GHz / 5GHz Wi-Fi、`ath10k`、`hostapd/wpad`、LuCI 和可选漫游辅助能力

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

项目刚初始化，尚未开始构建工作流和 `files/` 定制。

## License

MIT
