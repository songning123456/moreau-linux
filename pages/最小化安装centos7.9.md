```
下载Minimal版本,下载地址:http://mirrors.163.com/
yum install -y  wget
yum install -y net-tools
yum install -y vim
关闭防火墙、关闭selinux
装完做快照
```

### 常见问题
#### 1.最好装的时候就设置网卡开机自启动  

#### 2.使用yum 安装时出现 ： Loaded plugins: fastestmirror  
使用yum 安装是出现 ： Loaded plugins: fastestmirror   
```shell
[root@localhost yum.repos.d]# yum –y install httpd  httpd-devel
Loaded plugins: fastestmirror
```

解决办法：  

1.修改插件配置文件  

```shell
[root@localhost yum.repos.d]# vim  /etc/yum/pluginconf.d/fastestmirror.conf

[main]
enabled=0   //由 1 改成0 ，禁用该插件
verbose=0
always_print_best_host = true
socket_timeout=3
#  Relative paths are relative to the cachedir (and so works for users as well
# as root).
hostfilepath=timedhosts.txt
maxhostfileage=10
maxthreads=15
#exclude=.gov, facebook
#include_only=.nl,.de,.uk,.ie
```

 

2.修改yum 配置文件  

```shell
[root@localhost yum.repos.d]# vim /etc/yum.conf

9 plugins=0  //不使用插件
```

 

3. 清除缓存并重新构建yum 源  

```shell
[root@localhost yum.repos.d]# yum clean all

[root@localhost yum.repos.d]# yum makecache
```
 

4. 使用yum 重新安装   


#### 3.yum install Could not retrieve mirrorlist 解决
![](https://images.gitee.com/uploads/images/2020/1116/192826_7bc57d88_1479682.png)  

这个是个网络问题  
1.能不能ping通外网ip,如百度的某一个ip  
2. 能不能ping 通域名,  
3. 能不能ping通宿主主机  
首先保证这三个可以,即自己的网络没有问题,ping不同主机可能是因为主机的防火墙问题  

切换镜像使用阿里云的yum镜像  

我是发现网卡没启用,用`vi /etc/sysconfig/network-script/ifcfg-ens33`,设置了网卡信息，重启网卡就好了  