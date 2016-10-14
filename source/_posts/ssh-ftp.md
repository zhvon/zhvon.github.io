---
title: ssh+ftp
tags:
  - ssh
  - ftp
categories:
  - ssh
  - ftp
date: 2016-10-13 11:38:44
---
<Excerpt in index | 首页摘要> 
记录下
+ ``ssh`` 免密码登录配置
+ 搭建简单的 ``ftp`` 服务器 文件传输

如下假设
+ ``机器A``为本地机器（需要安装``ssh``，生成``sshkey``）
+ ``机器B``为远程机器 也是ftp服务器（需要安装``ssh`` 安装``vsftpd``）

# ssh 登录配置 #
## 添加用户 ##
``机器B``中添加新用户，操作如下	：
```bash
# -s 用户登录的shell
# -d 用户登录所在的目录
# -m 若目录不存在则自动创建
useradd -s /bin/bash -d /home/test -m test

# 修改test的密码
passwd test

# 添加到test到sudo用户组 拥有sudo权限 如果不需要就不执行这条
usermod -G sudo test 

# 测试 尝试切换到新用户
su test

```
<!-- more -->
<The rest of contents | 余下全文>
## 生成sshkey ##
``机器A``中生成sshkey，操作如下	：
```bash 

# 利用 key-gen 生成sshkey 默认用rsa加密算法
ssh-keygen -t [rsa|dsa]		#3个回车 密码为空

# 最后得到了两个文件：id_rsa和id_rsa.pub，在~/.ssh下
```

## 授权和ssh配置 ##
+ 将``机器A``中的``id_rsa.pub``(public key)内容copy到``机器B``中的``/home/test/.ssh/authorized_keys``(必须在用户[此处是test]目录下的.ssh文件夹)
+ 编辑 /etc/ssh/sshd_config 

```bash
# port有需要的话可以修改
5 Port 22

# 去掉此行注释
31 AuthorizedKeysFile      %h/.ssh/authorized_keys

# 禁止ssh登陆root
85 PermitRootLogin no

# ssh登陆不用密码
87 PasswordAuthentication no

# 修改完后 重启ssh
/etc/init.d/ssh restart

```

## 测试登陆 ##
```bash
ssh test@www.host.com[192.168.1.2]
```

# ftp 登录配置 #
``机器B``
## 安装配置 ##
```bash
# Ubuntu 14 LTS 使用 apt-get install
apt-get install -y vsftpd

# 重启服务测试是否安装成功
service vsftpd restart

# 备份conf文件 然后进行配置
cp /etc/vsftpd.conf /etc/vsftpd.conf.bak

# 打开配置文件 在文件末端添加以下配置
vim /etc/vsftpd.conf
```
> ``anonymous_enable=NO``
``local_enable=YES``
``chroot_local_user=YES``
``chroot_list_enable=YES``
``chroot_list_file=/etc/vsftpd.chroot_list``
``write_enable=YES``
``allow_writeable_chroot=YES``

重启服务
```bash
service vsftpd restart
```

## 目录权限 ##
```bash
# 假设ftp目录为/home/ftp
# 如果ftp目录不是属于当前用户 执行以下指令
chown -R test /home/ftp

# 修改ftp目录权限
chmod -R 775 /home/ftp
```

---

enjoy it。
