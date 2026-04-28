# 查看当前系统运行时长
```shell
uptime -p
```

# 查看最后一次启动时间
```shell
who -b
```

# 查看重启日期
```shell
last -x reboot
```

# 查看最后 N 次重启日期
```shell
last -x reboot | head -n
```

# 查看关机日期
```shell
last -x shutdown
```

# 查看最后 N 次关机日期
```shell
last -x shutdown | head -n
```

# 创建闹钟任务
```
命令：crontab -e
名称：cron 表达式
解释：在一个 cron 表达式中，前面的五个字段表示时间参数，分别是分钟、小时、月份中的日期、月份、星期几。这些参数用于指定任务执行的时间。
* * * * *
- - - - -
| | | | |
| | | | +----- 星期几 (0 - 7) (星期天为0或7)
| | | +------- 月份中的日期 (1 - 31)
| | +--------- 月份 (1 - 12)
| +----------- 小时 (0 - 23)
+------------- 分钟 (0 - 59)

举例：0 21 * * * docker restart xiaoya
解释：在每天的第 21 小时，即晚上 9 点，执行指定的命令 docker restart xiaoya
举例：0 7 * * * /sbin/reboot
解释：每天凌晨 7 点复位
```

# 连续输入 N 条相同的信息
```shell
yes "你好" | head -n 10
```
![image](https://github.com/user-attachments/assets/3a2f2e87-aeb4-4efd-ac36-cac1597edafa)
