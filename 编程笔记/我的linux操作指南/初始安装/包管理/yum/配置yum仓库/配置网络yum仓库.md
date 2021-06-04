```
自己准备一些软件包，然后构建一个yum的服务端仓库，然后通过http协议给本地机器走http协议访问。
 
yum server:
 
1、创建一个文件系统目录，上传一些软件包(模拟测试用的)
[root@node2 ~]# mkdir -p /data/yum_server
[root@node2 ~]# cd /data/yum_server
[root@node2 yum_server]# ls /data/yum_server
DenyHosts-2.6.tar.gz           nginx-1.10.3.tar.gz   redis-3.2.11.tar.gz  redis-4.0.3.tar.gz  redis-4.0.9.tar.gz
extundelete-0.2.4.tar.bz2      nginx-1.12.2.tar.gz   redis-3.2.3.tar.gz   redis-4.0.4.tar.gz  ss5-3.8.9-8.tar.gz
fping-2.4b2-10.el6.x86_64.rpm  nginx-1.14.0.tar.gz   redis-3.2.8.tar.gz   redis-4.0.5.tar.gz  tcl8.6.6-src.tar.gz
keepalived-1.2.20.tar.gz       nginx-1.6.3.tar.gz    redis-4.0.0.tar.gz   redis-4.0.6.tar.gz
kubernetes-1.11.2.zip          Python-2.7.13.tar.xz  redis-4.0.1.tar.gz   redis-4.0.7.tar.gz
lynis-2.5.7.tar.gz             redis-3.2.10.tar.gz   redis-4.0.2.tar.gz   redis-4.0.8.tar.gz
 
2、安装createrepo
[root@node2 yum_server]# rpm -q createrepo
package createrepo is not installed
[root@node2 yum_server]# yum install createrepo
......
 
3、 初始化repodata索引文件
-d --database
       Generate sqlite databases for use with yum. This is now the default.
-p --pretty
       Output xml files in pretty format.
-o --outputdir <url>
       Optional output directory (useful for read only media).
 
[root@node2 yum_server]# createrepo -pdo /data/yum_server/ /data/yum_server/
Spawning worker 0 with 1 pkgs
Workers Finished
Saving Primary metadata
Saving file lists metadata
Saving other metadata
Generating sqlite DBs
Sqlite DBs complete
[root@node2 yum_server]# ls /data/yum_server/
DenyHosts-2.6.tar.gz           nginx-1.10.3.tar.gz   redis-3.2.11.tar.gz  redis-4.0.3.tar.gz  redis-4.0.9.tar.gz
extundelete-0.2.4.tar.bz2      nginx-1.12.2.tar.gz   redis-3.2.3.tar.gz   redis-4.0.4.tar.gz  repodata
fping-2.4b2-10.el6.x86_64.rpm  nginx-1.14.0.tar.gz   redis-3.2.8.tar.gz   redis-4.0.5.tar.gz  ss5-3.8.9-8.tar.gz
keepalived-1.2.20.tar.gz       nginx-1.6.3.tar.gz    redis-4.0.0.tar.gz   redis-4.0.6.tar.gz  tcl8.6.6-src.tar.gz
kubernetes-1.11.2.zip          Python-2.7.13.tar.xz  redis-4.0.1.tar.gz   redis-4.0.7.tar.gz
lynis-2.5.7.tar.gz             redis-3.2.10.tar.gz   redis-4.0.2.tar.gz   redis-4.0.8.tar.gz
[root@node2 yum_server]# ls /data/yum_server/repodata
0dd81bd3480e1b700ef4d8501952ada4c17f0f7aa6e33b2ecdc1986327bcef70-primary.sqlite.bz2
54b9a1903b8bd39bcfd5c1e35840a19891a1187d078a009b4882aedf24e21154-filelists.sqlite.bz2
59026382882aae681f9af6f060b7d151f36be0e82583dcd07652cc7bb74d9f8c-other.sqlite.bz2
71b15f14ece520f2722a1648481a433c30e4bff9e407b89ad4d22f0ec40b90bd-other.xml.gz
cb1c34973cff8ddb6b6f40fc48ddaec468c057f9ff62c20fad26119d278b6c85-primary.xml.gz
dc8a69e32b8fbfd2a3d54f6f92ed53561754e3bf68b4263be3e74323a1bb2549-filelists.xml.gz
repomd.xml
[root@node2 yum_server]# cat /data/yum_server/repodata/repomd.xml |wc -l
55
 
4、通过http协议实现yum的文件传输
(1) 安装nginx
yum install nginx
此步骤要包装本地配置的yum客户端配置指向的启用的yum源，要有nginx安装包,常见的EPEL源和nginx官方自己的
yum仓库就提供nginx的软件包；
 
(2) 修改nginx主页指向
 
vim /etc/nginx/conf.d/default.conf
默认 root   /usr/share/nginx/html
改为
root /data/yum_server
(3) 设置nginx主页权限和防火墙放行http服务(我没有启用Selinux机制)
[root@node2 ~]# id nginx
uid=998(nginx) gid=996(nginx) groups=996(nginx)
[root@node2 ~]# setfacl -R -m u:nginx:r-- /data/yum_server
[root@node2 ~]# getfacl /data/yum_server
getfacl: Removing leading '/' from absolute path names
# file: data/yum_server
# owner: root
# group: root
user::rwx
user:nginx:r--
group::r-x
mask::r-x
other::r-x
[root@node2 ~]# getfacl /data/yum_server/redis-4.0.1.tar.gz 
getfacl: Removing leading '/' from absolute path names
# file: data/yum_server/redis-4.0.1.tar.gz
# owner: root
# group: root
user::rw-
user:nginx:r--
group::r--
mask::r--
other::r--
[root@node2 yum.repos.d]#  getfacl -p /data/yum_server/redis-4.0.1.tar.gz 
# file: /data/yum_server/redis-4.0.1.tar.gz
# owner: root
# group: root
user::rw-
user:nginx:r--
group::r--
mask::r--
other::r--
 
[root@node2 yum.repos.d]#  getfacl -p /data/yum_server/nginx-1.14.0.tar.gz 
# file: /data/yum_server/nginx-1.14.0.tar.gz
# owner: root
# group: root
user::rw-
user:nginx:r--
group::r--
mask::r--
other::r--
 
Firewalld防火墙放行http：
[root@node2 yum.repos.d]# firewall-cmd --help|grep add-service
  --add-service=<service>
[root@node2 yum.repos.d]# firewall-cmd --add-service=http --permanent
success
[root@node2 yum.repos.d]# firewall-cmd --reload
success
[root@node2 yum.repos.d]# firewall-cmd --list-services
dhcpv6-client http ssh
 
(5) 启动nginx，尝试机房访问测试
[root@node2 ~]# nginx -t 
nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
nginx: configuration file /etc/nginx/nginx.conf test is successful
 
[root@node2 ~]# systemctl enable nginx.service
Created symlink from /etc/systemd/system/multi-user.target.wants/nginx.service to /usr/lib/systemd/system/nginx.service.
[root@node2 ~]# systemctl start nginx.service
[root@node2 ~]# ss -nlt|grep :80
LISTEN     0      128                       *:80                       *:* 
 
如果是单纯的文件访问没问题，不过要想列出nginx的文件列表，首先nginx的工作进程用户要
对主页的那个目录有执行权限，其次，对应的站点的配置要添加以下内容：
autoindex on;  # 开启目录文件列表
autoindex_exact_size on;  # 显示出文件的确切大小，单位是bytes
autoindex_localtime on;  # 显示的文件时间为文件的服务器时间
charset utf-8,gbk;  # 避免中文乱码
 
给执行权限：
[root@node2 ~]# setfacl -R -m u:nginx:r-x /data/yum_server
[root@node2 ~]# getfacl -p /data/yum_server
# file: /data/yum_server
# owner: root
# group: root
user::rwx
user:nginx:r-x
group::r-x
mask::r-x
other::r-x
nginx -t检测语法，成功后，
systemctl reload nginx.service 重新加载
 
站点浏览器访问测试![](http://i2.51cto.com/images/blog/201810/28/584f59f82b3f4df557b5f0fae32705a0.png?x-oss-process=image/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_100,g_se,x_10,y_10,shadow_90,type_ZmFuZ3poZW5naGVpdGk=)
 
(6)、准备另外一台主机，配置客户端yum源的baseurl指向上面配置的http站点
 
客户端配置：
[local-http]
name=centos7 local http
baseurl=http://172.168.110.21/
enable=1
gpgcheck=0
 
这里有些凌乱了，我把源码包传到我的yum服务器的软件包目录了，我应该准备rpm包。
拷贝一些光盘的软件包到我的yum服务端的对应目录。
[root@node2 yum_server]# cp /media/cdrom/Packages/*.rpm .
[root@node2 yum_server]# du -sh /data/yum_server/
3.6G    /data/yum_server/
重新授权：
[root@node2 yum_server]# setfacl -R -m u:nginx:r-x /data/yum_server
软件包加入或变更，要手动更新一下：（更新是非常吃cpu资源的，请不要在有业务的生成环境上测试）
[root@node2 yum_server]# createrepo --update /data/yum_server
Spawning worker 0 with 3576 pkgs
Workers Finished
Saving Primary metadata
Saving file lists metadata
Saving other metadata
Generating sqlite DBs
Sqlite DBs complete
客户端重启构建一下缓存数据：
[root@node1 yum.repos.d]# yum makecache
Loaded plugins: fastestmirror
local-http                                                                                             | 2.9 kB  00:00:00     
(1/3): local-http/primary_db                                                                           | 2.7 MB  00:00:00     
(2/3): local-http/filelists_db                                                                         | 2.7 MB  00:00:00     
(3/3): local-http/other_db                                                                             | 1.1 MB  00:00:00     
Loading mirror speeds from cached hostfile
Metadata Cache Created
 
我之前把系统默认自带的repo都备份了，现在哪台客户端主机只剩下这个配置的http的yum仓库。
[root@node1 yum.repos.d]# yum repolist
Loaded plugins: fastestmirror
Loading mirror speeds from cached hostfile
repo id                                                 repo name                                                       status
local-http                                              centos7 local http                                              3,577
repolist: 3,577
[root@node1 yum.repos.d]# yum info zsh
Loaded plugins: fastestmirror
Loading mirror speeds from cached hostfile
Available Packages
Name        : zsh
Arch        : x86_64
Version     : 5.0.2
Release     : 7.el7
Size        : 2.4 M
Repo        : local-http
Summary     : Powerful interactive shell
URL         : http://zsh.sourceforge.net/
License     : MIT
Description : The zsh shell is a command interpreter usable as an interactive login
            : shell and as a shell script command processor.  Zsh resembles the ksh
            : shell (the Korn shell), but includes many enhancements.  Zsh supports
            : command line editing, built-in spelling correction, programmable
            : command completion, shell functions (with autoloading), a history
            : mechanism, and more.
[root@node1 yum.repos.d]# yum install zsh
......
[root@node1 yum.repos.d]# rpm -q zsh
zsh-5.0.2-7.el7.x86_64
经过简单测试后，发现没有问题。
 
PS：一般构建自己的yum服务器，会去外网同步更新yum仓库的软件包(这台服务器必须要保证出口带宽)。如果
走外网提供yum文件传输的话，可以这样做，同步外网的软件包，然后对内走内网使用yum仓库。
比较有名的，CentOS的自己的源，163的源，EPEL的源，阿里云的源等等。
```

