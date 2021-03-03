## yum介绍

yum可以解决rpm安装**软件包依赖**的问题

yum是rpm的前端工具实现，可以从指定服务器上自动下载程序包，并自动分析程序包的元数据、自动处理程序包之间的依赖关系，能**一次性安装完所有依赖的包**，而无须繁琐地一次次安装所有依赖包。yum访问访问文件服务器(俗称yum仓库),还可以使用光盘作为本地仓库或者自己制作本地仓库

yum客户端

```
配置文件：
    /etc/yum.conf：为所有仓库提供公共配置
    /etc/yum.repos.d/*.repo：为仓库的指向提供配置
 
yum仓库指向的配置文件：
[repositoryID]    这个表示仓库的id表示，不同的yum仓库配置不能相同；
name=Some name for this repository    这个是仓库的名字；
baseurl=url://path/to/repository/   这个是访问真正的yum服务端的url指向；
enabled={1|0}   仓库是否启用，1表示启用(默认值)，0表示未启用；
gpgcheck={1|0}   是否要对程序包数据的来源合法性和数据完整性做校验；，1表示检查，0表示不检查；
gpgkey=URL     指定GPG密钥文件的访问路径，可由仓库提供；；(如果gpgcheck启用，这里就要指向)
enablegroups={1|0}  是否允许以组的方式管理仓库；
failovermethod={roundrobin|priority}
    默认为：roundrobin，意为随机挑选；
    当baseurl同时指向多个仓库路径时，可指定以什么方式选择url去访问仓库，以及当某一路径访问
失败时，可指定如何再选择路径；roundrobin是随机挑选路径访问，priority是自上而下选择路径访
问；
cost=
    开销；开销越小，该仓库url更优；默认值为1000.
```

yum服务端

![](C:\Users\junji\OneDrive\md文档\我的linux操作指南\初始安装\包管理\9876d6412f090e1bb1a17bd8df439ea9.png)

![](C:\Users\junji\Desktop\9949c74e459fcb61367405286a9110b3.png)

