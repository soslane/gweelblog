**添加Samba服务器作为文件服务器，工作组名为WORKGROUP，发布共享目录/share，共享名为public，这个共享目录允许公司所有员工访问。**

## 安装 Samba
`sudo yum install samba -y`

## 创建共享目录
`mkdir /share`

## 编辑配置文件 /etc/samba/smb.conf  
`vim /etc/samba/smb.conf`

![](https://jsd.cdn.zzko.cn/gh/soslane/picgo@main/path/20240524142540.png)

## 设置 SELinux 上下文
```
yum install policycoreutils-python -y
semanage fcontext -a -t samba_share_t '/share(/.*)?'
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
Linux 系统  `smbclient //目标IP地址或主机名/共享目录 -U 用户名%密码`