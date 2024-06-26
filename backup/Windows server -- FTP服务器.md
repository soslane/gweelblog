## 一、FTP服务器

文件传输协议（File Transfer Protocol，FTP）是用来在两台计算机之间传输文件的通信协议，这两台计算机，一台是FTP服务器，一台是FTP客户端。FTP客户端可以从FTP服务器下载文件，也可以将文件上传到FTP服务器。默认端口21。

通常，用户需要使用FTP客户端软件（如FileZilla、WinSCP等）与FTP服务器建立连接，并提供有效的身份验证凭据（如用户名和密码）。一旦连接成功，用户就可以通过FTP客户端浏览服务器上的文件和目录，并执行所需的文件操作。

## 二、部署

在计算机“DNS1”上通过“服务器管理器”安装Web服务器（IIS）角色，同时也安装了FTP服务器。
在FTP服务器上创建一个新网站“ftp”，使用户在客户端计算机上能通过IP地址和域名进行访问。

 **1、创建使用IP地址访问的FTP站点**
创建使用IP地址访问的FTP站点的具体步骤如下。
    a、准备FTP主目录
在C盘上创建文件夹“C:\ftp”作为FTP主目录，并在其文件夹同存放一个文件“ftile1.txt”，供用户在客户端计算机上下载和上传测试。
    b、创建FTP站点

![https://jsd.cdn.zzko.cn/gh/soslane/picgo@main/path/20240522132224.png](https://jsd.cdn.zzko.cn/gh/soslane/picgo@main/path/20240522132224.png)

![https://jsd.cdn.zzko.cn/gh/soslane/picgo@main/path/20240522132300.png](https://jsd.cdn.zzko.cn/gh/soslane/picgo@main/path/20240522132300.png)

![https://jsd.cdn.zzko.cn/gh/soslane/picgo@main/path/20240522132433.png](https://jsd.cdn.zzko.cn/gh/soslane/picgo@main/path/20240522132433.png)

![https://jsd.cdn.zzko.cn/gh/soslane/picgo@main/path/20240522132545.png](https://jsd.cdn.zzko.cn/gh/soslane/picgo@main/path/20240522132545.png)

   c、测试站点

用户在客户端计算机win10-1上，打开浏览器或资源管理器，输入ftp://192.168.10.1就可以访问刚才建立的FTP站点。

**2、创建使用域名访问的FTP站点**

在DNS区域中创建别名

![https://jsd.cdn.zzko.cn/gh/soslane/picgo@main/path/20240522132709.png](https://jsd.cdn.zzko.cn/gh/soslane/picgo@main/path/20240522132709.png)

**3、使用不同的端口创建FTP站点**

a、在IIS管理器中，找到刚刚创建的FTP站点，在左侧的 "连接" 部分中选择该站点。
b、在右侧的 "操作" 面板中，选择 "绑定"。
c、在"站点绑定" 对话框中，点击 "添加"。
d、在 "添加站点绑定" 对话框中，选择 "FTP" 作为类型，然后输入您想要使用的端口号，例如2121。
e、单击 "确定" 完成端口设置。

**4、使用不同的IP创建FTP站点**

a、在 Windows Server 2016 中，您可以配置多个 IP 地址以供服务器使用。确保您的服务器有多个可用的 IP 地址，以便为 FTP 站点指定不同的 IP 地址。
b、在 IIS 管理器中，找到您刚刚创建的FTP站点，在左侧的"连接"部分中选择该站点。
c、在右侧的 "操作" 面板中，选择 "绑定"。
d、在 "站点绑定" 对话框中，点击 "添加"。
e、在 "添加站点绑定" 对话框中，选择 "FTP" 作为类型。
f、在 IP 地址下拉菜单中选择要为 FTP 站点使用的 IP 地址。
g、输入端口号，例如默认的 FTP 端口 21，然后单击 "确定"。

## 三、AD环境下的多用户隔离、磁盘配额

服务器已安装域服务、FTP服务（IIS），要求用户目录相互隔离（仅允许访问自己的目录而无法访问他人的目录），每个用户允许使用的FTP空间大小为100M，利用活动目录FTP隔离来实现。
原理：域用户登录到FTP站点时，FTP站点需要从Active Directory 域服务数据库中读取登陆用户的msIIS-FTPRoot与msIIS-FTPDir属性，以便得知其主目录的位置。但FTP站点需要提供有效的用户账户和密码，才可以读取这两个属性。需要建立一个域用户，赋予此用户读取登陆用户的这两个属性，并设置让FTP站点通过此账户来读取登陆用户的这两个属性。

1、创建组织单位及用户
方便进行实验，新建组织单位sales，并新建域用户salesuser1、salesuser2、salesmaster

2、设置委派控制的用户
右键sales——>委派任务——>添加salesmaster用户——>勾选“读取所用用户信息”——>完成

3、FTP服务器配置
a、C盘下新建FTP主目录FTP_sales，在主目录下再分别建立与用户名对应的文件夹salesuser1、salesuser2
b、添加ftp站点 （新建ftp站点时不允许匿名访问）
c、在IIS的FTP站点窗格中选择FTP用户隔离
d、在Active Directory中配置的FTP主目录设置成FTP_sales
e、服务器管理器——>工具——>ADSI编辑器——>操作——>连接到——>确定
f、找到ou=sales 右键salesuser1属性——>找到msIIS-FTPDir 设置为salesuser1，msIIS-FTPRoot 设置为c:\FTP_sales
g、使用同样的方式设置salesuser2

4、设置配额
右键C盘——>配额——>启动配额管理——>设置配额

5、测试