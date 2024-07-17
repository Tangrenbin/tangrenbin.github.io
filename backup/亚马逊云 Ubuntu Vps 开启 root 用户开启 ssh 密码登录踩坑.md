# ping 不通
 

1. 新建一个安全组，开启所有流量的出站和入站，特别是要添加一个  ICMP - IPv4

![image](https://github.com/user-attachments/assets/4a33d013-d1d2-4bba-b907-184f42b9d7fc)

![image](https://github.com/user-attachments/assets/5aa110cc-88b0-4df7-b0ad-a89ff139aced)

2. 先申请一个弹性ip然后绑定到实例，然后释放这个ip，达到刷新的目的

# 开启 root 用户开启 ssh 密码登录


1. 在控制台登录到终端，**登录之后不要轻易关闭网页，因为可能会导致无法再次进入**
2. 切换到root用户
```shell
sudo -i
```
3. 设置 root 用户密码
```shell
passwd
```
4. 编辑sshd_config配置文件，修改 PermitRootLogin 和 PasswordAuthentication 为 yes
```shell
vim /etc/ssh/sshd_config
```
5. 查看配置文件是否生效
```shell
sshd -T | grep permitrootlogin
sshd -T | grep passwordauthentication
```
6. 如果 passwordauthentication 还是 no , 那就说明还有其他的地方把这个参数配置成了 no ,通过这个命名把那个文件找出来，并且进去这个目录把这个参数改成 yes
```shell
grep -i include /etc/ssh/sshd_config
```
![image](https://github.com/user-attachments/assets/8d5d6e0a-2e78-49cc-aa7d-ab3c889e6d28)

7. 重启 SSH 服务来应用更改
```shell
systemctl restart ssh
```
8. 再次验证配置是否正确
```shell
sshd -T | grep passwordauthentication
```
![image](https://github.com/user-attachments/assets/5459b805-d926-42f8-a8ba-1d5892aa24a6)




