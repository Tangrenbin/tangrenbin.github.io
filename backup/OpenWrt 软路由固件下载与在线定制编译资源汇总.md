# OpenWrt 软路由固件下载与在线定制编译资源汇总

本文档整理了 OpenWrt 软路由常用的固件下载、在线定制编译平台及相关资源。

## 1. 🎯 官方资源

| 项目名称 | 网址/地址 | 特点 |
| :--- | :--- | :--- |
| **OpenWrt 官方** | [官网](https://openwrt.org/) <br> [下载站](https://downloads.openwrt.org/) | 官方原版固件，最稳定，纯净无插件，适合极客和有一定基础的用户。 |
| **ImmortalWrt** | [官网](https://immortalwrt.org/) <br> [GitHub](https://github.com/immortalwrt/immortalwrt) | 基于 OpenWrt 官方的分支，更新及时，支持更多硬件设备，有预编译固件下载。 |

## 2. 🔥 热门定制固件项目 (源码)

| 项目名称 | 网址/地址 | 特点 |
| :--- | :--- | :--- |
| **Lean's LEDE** | [GitHub](https://github.com/coolsnowwolf/lede) | 国内最流行的 OpenWrt 分支，集成大量常用插件（SSR+、AdGuard、网络唤醒等），适合国内用户习惯，需自行编译。 |
| **Flippy's OpenWrt** | [GitHub](https://github.com/unifreq/openwrt_packit) | 专注于 ARM 设备（如斐讯 N1、玩客云、S905/S912 盒子等），支持一键打包固件。 |
| **Sirpdboy 固件** | [GitHub](https://github.com/sirpdboy/openwrt) | 定期更新编译固件，集成常用插件，支持多种 x86 和 ARM 设备。 |

## 3. 🌐 在线编译平台

| 平台名称 | 网址 | 特点 |
| :--- | :--- | :--- |
| **Supes.top** (推荐) | [supes.top](https://supes.top/) <br> [openwrt.ai](https://openwrt.ai/) | 在线可视化定制编译，无需本地环境，选择插件即可生成，支持多种设备和架构，速度快。 |
| **OpenWrt Firmware Selector** | [firmware-selector.openwrt.org](https://firmware-selector.openwrt.org/) | OpenWrt 官方在线定制工具，可以添加自定义软件包，生成纯净版固件。 |
| **GitHub Actions** | [P3TERX/Actions-OpenWrt](https://github.com/P3TERX/Actions-OpenWrt) | 自动化编译模板。Fork 后修改配置文件，利用 GitHub Actions 云编译，完全免费，可自定义程度极高。 |

## 4. 📦 预编译固件下载站

| 网站名称 | 网址 | 特点 |
| :--- | :--- | :--- |
| **恩山无线论坛** | [right.com.cn/forum](https://www.right.com.cn/forum/) | 国内最大的路由器固件社区，大量网友分享各种设备的编译固件和教程。 |
| **KoolShare 固件** | [koolshare.cn](https://koolshare.cn/) | 老牌固件提供商，以 LEDE 和梅林固件为主，插件生态完善。 |

## 5. 🛠️ 常用插件项目

| 项目名称 | 网址 | 说明 |
| :--- | :--- | :--- |
| **OpenClash** | [GitHub](https://github.com/vernesong/OpenClash) | OpenWrt 上功能最强大的代理客户端之一。 |
| **PassWall / SSR+** | (通常集成在固件中) | 常用的科学上网插件，多见于 Lean's LEDE 等定制固件。 |

---

## 💡 推荐方案

- **新手入门**：推荐使用 **Supes.top** 在线编译，或下载 **ImmortalWrt** 预编译固件后手动安装所需插件。
- **进阶定制**：推荐 Fork **P3TERX/Actions-OpenWrt** 使用 GitHub Actions 进行云编译。
- **深度玩家**：推荐本地搭建环境编译 **Lean's LEDE** 源码。
- **ARM 盒子用户**：推荐关注 **Flippy** 的打包项目。
