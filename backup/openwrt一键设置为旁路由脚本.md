```shell
#!/bin/sh
# OpenWrt bypass-router quick setup: static IP, disable IPv6, disable DHCP

# Tunable parameters (override via env if needed)
LAN_DEV="${LAN_DEV:-br-lan}"
LAN_IP="${LAN_IP:-172.17.0.207}"
LAN_MASK="${LAN_MASK:-255.255.255.0}"
LAN_GW="${LAN_GW:-172.17.0.1}"
LAN_DNS="${LAN_DNS:-61.134.1.4}"

echo "=========================================="
echo "Start bypass-router setup (LAN: ${LAN_IP})"
echo "=========================================="

echo "[1/5] Configure static IP..."
uci set network.lan.device="${LAN_DEV}"
uci set network.lan.proto='static'
uci set network.lan.ipaddr="${LAN_IP}"
uci set network.lan.netmask="${LAN_MASK}"
uci set network.lan.gateway="${LAN_GW}"
uci set network.lan.dns="${LAN_DNS}"

echo "[2/5] Disable IPv6..."
uci delete network.lan.ip6assign 2>/dev/null
uci set network.lan.ipv6='0'
uci set network.lan.delegate='0'
uci set network.globals.ula_prefix='' 2>/dev/null
uci commit network

echo "[3/5] Disable DHCP / IPv6 services..."
uci set dhcp.lan.ignore='1'
uci set dhcp.lan.dhcpv6='disabled'
uci set dhcp.lan.ra='disabled'
uci delete dhcp.lan.ndp 2>/dev/null
uci commit dhcp

echo "[4/5] Adjust firewall (LAN ACCEPT; do not open WAN)..."
uci set firewall.lan.input='ACCEPT'
uci set firewall.lan.output='ACCEPT'
uci set firewall.lan.forward='ACCEPT'
uci commit firewall

echo "[5/5] Stop IPv6 related services..."
/etc/init.d/odhcpd stop
/etc/init.d/odhcpd disable

echo "Restarting network services..."
/etc/init.d/network restart
sleep 3
/etc/init.d/dnsmasq restart
/etc/init.d/firewall restart

echo "=========================================="
echo "Configuration complete, verifying:"
echo "=========================================="
echo "Interface ${LAN_DEV} address:"
ip addr show "${LAN_DEV}" | grep -E "inet "
echo ""
echo "Ping gateway:"
ping -c 3 "${LAN_GW}"
echo ""
echo "DHCP status:"
uci show dhcp.lan | grep ignore
echo ""
echo "IPv6 status:"
uci show network.lan | grep -E "ipv6|ip6|ula"
echo "=========================================="
echo "Bypass-router setup done!"
echo "=========================================="

```