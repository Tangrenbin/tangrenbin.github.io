# Gemini Chat Shell 客户端

这是一个基于 Shell 实现的 Gemini AI 聊天客户端，支持多个 Gemini 模型，提供简单直观的命令行界面。

## 功能特点

- 🔄 自动获取最新的 Gemini 模型列表
- 🤖 支持多个 Gemini 模型系列（Pro/Flash）
- 🔄 支持实时切换和刷新模型
- 📦 自动检查并安装依赖
- 🚀 简单易用的命令行界面

## 系统要求

- Linux/Unix 系统或 WSL（Windows Subsystem for Linux）
- bash shell
- 网络连接

## 依赖项

脚本会自动检查并安装以下依赖：
- curl：用于发送 HTTP 请求
- jq：用于解析 JSON 数据

## 安装和运行

1. 下载脚本：

复制脚本内容到本地文件


2. 添加执行权限：
```bash
chmod +x gemini_chat.sh
```

3. 运行脚本：
```bash
./gemini_chat.sh
```

## 使用说明

### 基本操作

1. 启动后，脚本会自动获取并显示可用的模型列表
2. 可以通过输入编号选择模型，或直接回车使用默认模型（最新版本）
3. 在聊天界面中：
   - 直接输入文字进行对话
   - 输入 `退出` 或 `quit` 或 `exit` 结束对话
   - 输入 `切换` 可以切换到其他模型
   - 输入 `刷新` 可以刷新模型列表

### 模型说明

- pro 系列：响应质量更高，适合需要深度思考的场景
- flash 系列：响应速度更快，适合日常对话
- 版本号越大代表模型越新

## 注意事项

1. 首次运行时，如果缺少依赖，脚本会尝试自动安装
2. 安装依赖时可能需要 sudo 权限
3. 确保系统有稳定的网络连接
4. API 密钥已内置，无需额外配置

## 常见问题

1. 如果遇到权限问题：
   ```bash
   sudo ./gemini_chat.sh
   ```

2. 如果遇到网络问题，请检查：
   - 网络连接是否正常
   - 是否可以访问 API 服务器
   - 是否有防火墙限制

3. 如果遇到依赖安装失败：
   ```bash
   # 手动安装依赖
   sudo apt-get update
   sudo apt-get install -y curl jq
   ```

## 更新日志

### v1.0.0
- 初始版本发布
- 支持动态获取模型列表
- 支持模型切换和刷新
- 添加自动依赖安装功能

```shell

#!/bin/bash

# Gemini API配置
API_KEY="AIzaSyDOLKXvMTqw0EgLHVOA13fgBZHbS8NEvBs"
BASE_URL="https://gemini.tangrenbin.us.kg"

# 声明关联数组来存储模型
declare -A MODELS

# 获取可用模型列表
fetch_models() {
    echo "正在获取可用模型列表..."
    
    # 发送请求获取模型列表
    response=$(curl -s -X GET "$BASE_URL/v1/models" \
        -H "Authorization: Bearer $API_KEY")
    
    # 检查响应是否成功
    if [ $? -ne 0 ]; then
        echo "错误: 无法连接到API服务器"
        exit 1
    fi
    
    # 使用jq解析JSON响应，获取模型列表
    models_data=$(echo "$response" | jq -r '.data[] | select(.id | contains("gemini")) | .id' 2>/dev/null)
    
    if [ -z "$models_data" ]; then
        echo "错误: 无法获取模型列表或解析响应"
        echo "API返回: $response"
        exit 1
    fi
    
    # 清空现有模型列表
    MODELS=()
    
    # 将模型添加到关联数组
    index=1
    while IFS= read -r model; do
        MODELS["$index"]="$model"
        ((index++))
    done <<< "$models_data"
    
    # 设置默认模型为最后一个（通常是最新的版本）
    DEFAULT_MODEL="$((index-1))"
}

# 打印模型列表
print_models() {
    echo -e "\n可用模型列表："
    echo "注意：不同模型有不同的特点："
    echo "- pro系列：响应质量更高"
    echo "- flash系列：响应速度更快"
    echo "- 数字越大代表版本越新"
    echo -e "- 默认使用最新的模型: ${MODELS[$DEFAULT_MODEL]}\n"
    
    # 使用循环按数字顺序显示
    for ((i=1; i<=${#MODELS[@]}; i++)); do
        if [ "$i" == "$DEFAULT_MODEL" ]; then
            echo "$i. ${MODELS[$i]} (默认)"
        else
            echo "$i. ${MODELS[$i]}"
        fi
    done
}

# 发送聊天请求
send_chat_request() {
    local model=$1
    local message=$2
    
    response=$(curl -s -X POST "$BASE_URL/v1/chat/completions" \
        -H "Content-Type: application/json" \
        -H "Authorization: Bearer $API_KEY" \
        -d '{
            "model": "'"$model"'",
            "messages": [
                {"role": "system", "content": "你是一个有帮助的AI助手。"},
                {"role": "user", "content": "'"$message"'"}
            ],
            "stream": false,
            "temperature": 0.7,
            "max_tokens": 2048
        }')
    
    # 提取响应内容
    content=$(echo "$response" | jq -r '.choices[0].message.content' 2>/dev/null)
    
    if [ -n "$content" ] && [ "$content" != "null" ]; then
        echo -e "\nAI: $content"
    else
        echo -e "\n错误: 无法获取有效响应"
        echo "API返回: $response"
    fi
}

# 主对话循环
main() {
    # 获取可用模型列表
    fetch_models
    
    print_models
    echo -e "\n直接按回车使用默认模型(${MODELS[$DEFAULT_MODEL]})"
    read -p "请选择模型编号 (1-${#MODELS[@]}): " model_choice
    
    # 如果直接按回车，使用默认模型
    if [ -z "$model_choice" ]; then
        model_choice=$DEFAULT_MODEL
    fi
    
    # 验证模型选择
    while [ -z "${MODELS[$model_choice]}" ]; do
        echo "无效的选择，请重试。"
        read -p "请选择模型编号 (1-${#MODELS[@]}): " model_choice
        if [ -z "$model_choice" ]; then
            model_choice=$DEFAULT_MODEL
        fi
    done
    
    selected_model=${MODELS[$model_choice]}
    echo -e "\n已选择模型: $selected_model"
    echo -e "\n开始对话 (输入'退出'结束对话，输入'切换'可以切换模型，输入'刷新'可以重新获取模型列表)"
    
    while true; do
        echo -en "\n你: "
        read user_input
        
        case "$user_input" in
            退出|quit|exit)
                echo -e "\n感谢使用！再见！"
                break
                ;;
            切换)
                main
                return
                ;;
            刷新)
                fetch_models
                print_models
                echo -e "\n继续使用当前模型: $selected_model"
                ;;
            *)
                if [ -n "$user_input" ]; then
                    echo "正在等待AI响应..."
                    send_chat_request "$selected_model" "$user_input"
                fi
                ;;
        esac
    done
}

# 检查并安装必要的依赖
check_dependencies() {
    local missing_deps=()
    
    if ! command -v curl &> /dev/null; then
        missing_deps+=("curl")
    fi
    
    if ! command -v jq &> /dev/null; then
        missing_deps+=("jq")
    fi
    
    if [ ${#missing_deps[@]} -ne 0 ]; then
        echo "检测到缺少必要的依赖项:"
        for dep in "${missing_deps[@]}"; do
            echo "- $dep"
        done
        
        # 检查是否有root权限
        if [ "$EUID" -ne 0 ]; then
            echo "正在使用sudo安装依赖..."
            for dep in "${missing_deps[@]}"; do
                echo "正在安装 $dep..."
                sudo apt-get update -qq && sudo apt-get install -y "$dep"
                if [ $? -ne 0 ]; then
                    echo "错误: 安装 $dep 失败"
                    exit 1
                fi
            done
        else
            echo "正在安装依赖..."
            for dep in "${missing_deps[@]}"; do
                echo "正在安装 $dep..."
                apt-get update -qq && apt-get install -y "$dep"
                if [ $? -ne 0 ]; then
                    echo "错误: 安装 $dep 失败"
                    exit 1
                fi
            done
        fi
        
        echo "所有依赖已安装完成"
    fi
}

# 运行程序
check_dependencies
main 

```