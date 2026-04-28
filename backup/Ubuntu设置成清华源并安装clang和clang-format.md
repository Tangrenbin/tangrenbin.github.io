```shell
#!/bin/bash

echo "=== 配置清华大学镜像源安装 clang ==="

# 备份原始源
sudo cp /etc/apt/sources.list /etc/apt/sources.list.backup
echo "✓ 已备份原始源配置到 /etc/apt/sources.list.backup"

# 获取Ubuntu版本代号
UBUNTU_VERSION=$(lsb_release -cs)
echo "检测到Ubuntu版本: $UBUNTU_VERSION"

# 写入清华大学镜像源配置
cat << EOF | sudo tee /etc/apt/sources.list
# 清华大学开源软件镜像站
# 默认注释了源码镜像以提高 apt update 速度，如有需要可自行取消注释
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ $UBUNTU_VERSION main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ $UBUNTU_VERSION main restricted universe multiverse
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ $UBUNTU_VERSION-updates main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ $UBUNTU_VERSION-updates main restricted universe multiverse
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ $UBUNTU_VERSION-backports main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ $UBUNTU_VERSION-backports main restricted universe multiverse
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ $UBUNTU_VERSION-security main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ $UBUNTU_VERSION-security main restricted universe multiverse

# 预发布软件源，不建议启用
# deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ $UBUNTU_VERSION-proposed main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ $UBUNTU_VERSION-proposed main restricted universe multiverse
EOF

echo "✓ 已配置清华大学镜像源"

# 更新软件包列表
echo "正在更新软件包列表..."
sudo apt update

if [ $? -eq 0 ]; then
    echo "✓ 软件包列表更新成功"
else
    echo "❌ 软件包列表更新失败，可能网络有问题"
    exit 1
fi

# 搜索可用的clang版本
echo ""
echo "=== 可用的clang版本 ==="
apt-cache search "^clang-[0-9]" | sort

echo ""
echo "=== 安装clang和clang-format ==="

# 安装clang和clang-format
sudo apt install -y clang clang-format

# 检查安装结果
if [ $? -eq 0 ]; then
    echo "✓ clang和clang-format安装成功"
    
    # 验证安装
    echo ""
    echo "=== 安装验证 ==="
    echo "clang版本："
    clang --version
    echo ""
    echo "clang-format版本："
    clang-format --version
    
    echo ""
    echo "✅ 安装完成！"
else
    echo "❌ 安装失败"
    exit 1
fi

# 提供恢复原始源的命令
echo ""
echo "📝 如需恢复原始软件源，请运行："
echo "   sudo cp /etc/apt/sources.list.backup /etc/apt/sources.list"
echo "   sudo apt update"

echo ""
echo "🔧 如需安装特定版本的clang，可运行："
echo "   sudo apt install clang-11 clang-format-11  # 安装clang 11"
echo "   sudo apt install clang-12 clang-format-12  # 安装clang 12" 
```