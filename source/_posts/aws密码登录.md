---
title: aws密码登录
tags: aws
---

aws 默认不支持账号密码登录,有时会遇到账号密码登录的场景,按照如下步骤可完成
通过ssh直接登录到系统

1.设置当前用户的密码
```PowerShell
sudo passwd user #user当前登录系统的用户
```

2.修改配置文件
``` PowerShell
sudo vim /etc/ssh/sshd_config
```
修改如下三个地方
```
#PermitRootLogin prohibit-password
PermitRootLogin yes

# Change to no to disable tunnelled clear text passwords
PasswordAuthentication yes

# Set this to 'yes' to enable PAM authentication, account processing,
# and session processing. If this is enabled, PAM authentication will
# be allowed through the ChallengeResponseAuthentication and
# PasswordAuthentication.  Depending on your PAM configuration,
# PAM authentication via ChallengeResponseAuthentication may bypass
# the setting of "PermitRootLogin without-password".
# If you just want the PAM account and session checks to run without
# PAM authentication, then enable this but set PasswordAuthentication
# and ChallengeResponseAuthentication to 'no'.
UsePAM yes
```
3.重启服务
```
service sshd restart
```
4.换一台公钥不在服务器上的机器登录验证
```
ssh user@ip
```
会提示输入密码.输入第一次设置的密码即可登录到服务器