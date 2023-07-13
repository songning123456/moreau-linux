### systemd日志管理journal  
journal:systemd自己提供的日志工具和服务  
配置文件:`/etc/systemd/journald.conf`  
```shell
#journal日志服务
systemctl list-units | grep systemd.journald.service
```

```shell
#systemd将日志信息交给journal服务,journal服务将日志存放到RAM(内存虚拟文件系统:tmpfs,目录挂载在/run下)
systemd --> journal  --> RAM

[root@ser1 cron.daily]# df -h
文件系统        容量  已用  可用 已用% 挂载点
devtmpfs        1.9G     0  1.9G    0% /dev
tmpfs           1.9G     0  1.9G    0% /dev/shm
tmpfs           1.9G   12M  1.9G    1% /run    #操作系统发生重启后,这个目录下的内容就会丢失,这里面是临时进行缓存来使用的
tmpfs           1.9G     0  1.9G    0% /sys/fs/cgroup
/dev/sda2        17G  2.7G   14G   17% /
/dev/sda1       976M  112M  798M   13% /boot
tmpfs           378M     0  378M    0% /run/user/0

```

日志级别  
![](https://images.gitee.com/uploads/images/2021/0202/214924_7f33168e_1479682.png)   

journalctl查看粗体的日志,日志级别就是在notice或warning级别  
![](https://images.gitee.com/uploads/images/2021/0202/215217_386a8310_1479682.png)  

journalctl查看红色的日志,日志级别在err级别以上的,也就是可能造成系统性的危害的日志
![](https://images.gitee.com/uploads/images/2021/0202/215301_b9e202ad_1479682.png)  


### journalctl查看内存日志  
journalctl 可以查看内存日志,这里我们看看常用的指令  

```shell
#查看全部
journalctl 
#查看最新3条
journal -n 3
#查看起始时间到结束时间的日志(可加日期)
journalctl --since 20:50 --until 21:00:10
#今天0点开始,到当前执行这条命令时间为止的日志
journalctl --since today
#查看今天0点到截止为30分钟之前的日志
journalctl --since today --until "30 min ago"
#查看今天0点到截止为2个小时之前的日志
journalctl --since today --until "2 hour ago"
#查看err级别的日志
journalctl -p err 
#查看err级别到alert级别的日志
journalctl -p err..alert
#日志详细内容
journalctl -o verbose
#查看某个服务的日志
journalctl -u sshd.service
#查看包含这些参数的日志,top命令可以查看PID和COMMAND
journalctl _PID=1245 _COMM=sshd
#或者:
journalctl | grep sshd
#和tail /var/log/messages -f  里面的-f一样,实时的发现日志,有日志则刷新
journalctl -f 
#查看操作系统启动的时候的日志
journalctl -b
#查看root用户的相关日志,这个可以根据/etc/passwd查看某个用户的UID
journalctl _UID=0
```

注意:journalctl  查看的是内存日志,重启清空  
演示案例:  
使用journalctl | grep sshd 来看看用户登录清空,重启系统,再次查询,看看日志有生命变化没有  
