```shell
#!/bin/bash

echo "Installing Chinese language pack..."

# Update package sources
opkg update

# Install core Chinese language packages
opkg install luci-i18n-base-zh-cn \
  luci-i18n-firewall-zh-cn

# Set default language
uci set luci.main.lang='zh_cn'
uci commit luci

# Clear cache
rm -rf /tmp/luci-*

# Restart service
/etc/init.d/uhttpd restart

echo "Installation complete! Please refresh your browser."
```