**添加Samba服务器作为文件服务器，工作组名为WORKGROUP，发布共享目录/share，共享名为public，这个共享目录允许公司所有员工访问。 ** 

## 安装 Samba
`sudo yum install samba -y`

## 创建共享目录
`mkdir /share`

## 编辑配置文件 /etc/samba/smb.conf  
`vim /etc/samba/smb.conf`

! [](https://jsd.cdn.zzko.cn/gh/soslane/picgo@main/path/20240524142540.png)

## 设置 SELinux 上下文
```
chcon -t samba_share_t /share
restorecon -R /share
```

## 配置防火墙
```
firewall-cmd --permanent --zone=public --add-service=samba
firewall-cmd --reload
```

## 重启并启用 Samba 服务
`systemctl restart smb`

## 测试
Windows 打开 文件管理器输入 \\目标IP地址或主机名
Linux 系统 smbclient //目标IP地址或主机名/共享目录 -U 用户名%密码

## Share Definitions 其他设置参数
public = yes #允许匿名访问
public = no #禁止匿名访问

valid users = username #若存在重要数据, which requires auditing access users
valid users = @组名  

read only = yes   #只读
read only = no    #读写

writable = yes     #读写
writable = no      #只读

write list = 用户名
write list = @组名