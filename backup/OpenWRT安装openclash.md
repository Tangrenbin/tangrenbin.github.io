```shell
#!/bin/bash

# OpenClash 安装脚本
# 注意：执行前请将 luci-app-openclash_*.ipk 文件放在当前目录

# 移除标准版 dnsmasq（避免与 dnsmasq-full 冲突）
echo "正在移除标准版 dnsmasq..."
opkg remove dnsmasq

# 更新软件包列表
opkg update

# 方案1: iptables（推荐用于较旧的内核）
echo "开始安装 OpenClash 依赖（iptables 方案）..."
opkg install bash iptables dnsmasq-full curl ca-bundle ipset ip-full \
  iptables-mod-tproxy iptables-mod-extra ruby ruby-yaml \
  kmod-tun kmod-inet-diag unzip luci-compat luci luci-base

# 下载
wget -O luci-app-openclash_0.47.028_all.ipk https://github.com/vernesong/OpenClash/releases/download/v0.47.028/luci-app-openclash_0.47.028_all.ipk

# 安装 OpenClash
echo "安装 OpenClash..."
opkg install ./luci-app-openclash_0.47.028_all.ipk

echo "安装完成！"

# 如果你的系统使用 nftables（OpenWrt 22.03+），请注释掉上面的 iptables 方案，改用下面的：
# echo "开始安装 OpenClash 依赖（nftables 方案）..."
# opkg install bash dnsmasq-full curl ca-bundle ip-full ruby ruby-yaml \
#   kmod-tun kmod-inet-diag unzip kmod-nft-tproxy luci-compat luci luci-base
# opkg install ./luci-app-openclash_*.ipk
```