**Add Samba server as a file server, the workgroup name is WORKGROUP, publish the shared directory/share, the share name is public, this shared directory is accessible to all employees in the company. ** 

## Install Samba
`sudo yum install samba -y`

## Create a shared directory
`mkdir /share`

## 编辑配置文件 /etc/samba/smb.conf  
`vim /etc/samba/smb.conf`

! [](https://jsd.cdn.zzko.cn/gh/soslane/picgo@main/path/20240524142540.png)

## Set the SELinux context
```
yum install policycoreutils-python -y
chcon -t samba_share_t /share
restorecon -R /share
```

## Configure the firewall
```
firewall-cmd --permanent --zone=public --add-service=samba
firewall-cmd --reload
```

## Restart and enable the Samba service
'Systemctl restart SMB'

## Testing
Open Windows File Manager and enter \\destination IP address or hostname 
Linux 系统  `smbclient Destination IP address or hostname/shared directory -U Username% Password'

## Share Definitions Other configuration parameters
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