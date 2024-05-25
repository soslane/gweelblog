添加Samba服务器作为文件服务器，工作组名为WORKGROUP，发布共享目录/share，共享名为public，这个共享目录允许公司所有员工访问。 

## 安装 Samba
`sudo yum install samba -y`

## 创建共享目录
`mkdir /share`

## 编辑配置文件 /etc/samba/smb.conf  
`vim /etc/samba/smb.conf`

![](https://jsd.cdn.zzko.cn/gh/soslane/picgo@main/path/20240524142540.png)

## 设置 SELinux 上下文
```
chcon -t samba_share_t /share
restorecon -R /share
```

## 配置防火墙
```
firewall-cmd --permanent --add-service=samba
firewall-cmd --reload
```

## 重启并启用 Samba 服务
`systemctl restart smb`

## 测试
Windows 打开 文件管理器输入 \\目标IP地址或主机名   断开已连接的用户命令：`net use * /del`
Linux 系统 smbclient //目标IP地址或主机名/共享目录 -U 用户名%密码

## Share Definitions 其他设置参数
public = yes #允许匿名访问
public = no #禁止匿名访问

browseable = yes  #共享资源在网络浏览列表中可见
browseable = no   #共享资源在网络浏览列表中隐藏，如需访问通过完全路径访问

hosts allow = 192.168.10.    server.hello.com   #表示允许来自192.168.10网段或server.hello.com的主机访问
hosts deny =192.168.1.    #表示不允许192.168.1网段的主机访问。 如果allow和deny冲突，allow优先

valid users = username #指定哪些用户和组可以访问共享目录，多个用“,”隔开
valid users = @组名  

read only = yes   #只读
read only = no    #读写

write list = 用户名
write list = @组名


## 举个栗子
**建立共享目录student,它的本机路径为/home/student，只有teacher组的用户可以读写该目录，student用户只能读取**

**过程：**

安装samba服务器
`yum install samba -y`

为了实验方便，关闭Selinux
`vim /etc/selinux/config`        改成disabled

查看selinux状态   
`sestatus `

**1.创建组别**   
```
groupadd teacher
groupadd student
```
**2.新建用户并加入到组** 
```
useradd -G teacher user1
useradd -G student user2
```
**3.创建目录** 

`mkdir /home/student`

**4.更改目录所有者及权限**
```
chown user1:teacher /home/student
chmod 775 /home/student
```
**5.创建samba用户**
```
smbpasswd -a user1
smbpasswd -a user2
```
**6.编辑samba配置文件**

`vim /etc/samba/smb.conf`

![](https://jsd.cdn.zzko.cn/gh/soslane/picgo@main/path/20240525210536.png)
**7.设置防火墙**
```
firewall-cmd --permanent --add-service=samba
firewall-cmd --reload
```
**8.重启samba服务，并开启开机自启**
```
systemctl restart smb
systemctl enable smb
```
**9.测试**