 关于时区的概念，其实初中地理课已经涉及，很多人都多少了解一些，可能只是细节搞不太清楚。为什么会将地球分为不同时区呢？因为地球总是自西向东自转，东边总比西边先看到太阳，东边的时间也总比西边的早。东边时刻与西边时刻的差值不仅要以时计，而且还要以分和秒来计算。整个地球分为二十四时区，每个时区都有自己的本地时间。在国际无线电通信场合，为了统一起见，使用一个统一的时间，称为通用协调时(UTC, Universal Time Coordinated)。UTC与格林尼治平均时(GMT, Greenwich Mean Time)一样，都与英国伦敦的本地时相同。

关于时间的几个标准，如下所示，具体可以Google、Baidu搜索一下。  

 CST：中国标准时间（China Standard Time），这个解释可能是针对RedHat Linux。  

 UTC：协调世界时，又称世界标准时间，简称UTC，从英文国际时间/法文协调时间”Universal Time/Temps Cordonné”而来。中国大陆、香港、澳门、台湾、蒙古国、新加坡、马来西亚、菲律宾、澳洲西部的时间与UTC的时差均为+8，也就是UTC+8。  

 GMT：格林尼治标准时间（旧译格林威治平均时间或格林威治标准时间；英语：Greenwich Mean Time，GMT）是指位于英国伦敦郊区的皇家格林尼治天文台的标准时间，因为本初子午线被定义在通过那里的经线。   

 关于时区分布图，大家可以参考http://www.timedate.cn/time/timezone.htm，我们国家跨越了东五区、东六区、东七区、东八区、东九区五个时区，一般都统一采用东八区计时时间。  

![](https://images.gitee.com/uploads/images/2020/0924/154554_3393ec40_1479682.png)  
![](https://images.gitee.com/uploads/images/2021/0204/122326_0a0fa1b7_1479682.png)  

### Linux系统如何查看设置所在的时区呢？  
1：使用date命令查看时区  
```shell
[root@db-server ~]# date -R
Sun, 11 Jan 2015 07:10:28 -0800
[root@db-server ~]# 
```

如上RFC 2822 format所示，上面命令输出了-0800表示西八区，是美国旧金山所在的时区，下面表示我们国家的东八区（+0800）  
```shell
[root@lnx01 ~]# date -R
Sun, 11 Jan 2015 23:06:02 +0800
```

修改时区  
| Centos6  |  Centos7(Mininal,ECS)  |
|---|---|
| `cp /usr/share/zoneinfo/Asia/Shanghai /etc/localtime` | `timedatectl`  |

Centos6、7都适用  
```shell
#先备份时区文件
cd /etc
cp localtime localtime_bak
#\cp 覆盖不提示 
\cp /usr/share/zoneinfo/Asia/Tokyo /etc/localtime

#(这里用的东京的时区,东9区)
[root@manager etc]# date -R
Thu, 04 Feb 2021 13:45:29 +0900
#改回东8区
\cp localtime_bak localtime
date -R
#也可以做软链接改变时区,这里的-f代表如果文件存在则覆盖
ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
```

Centos7 
```shell
[root@manager etc]# timedatectl
      Local time: 四 2021-02-04 12:49:16 CST #本地时间
  Universal time: 四 2021-02-04 04:49:16 UTC #世界时间
        RTC time: 四 2021-02-04 12:49:16  #主板时间
       Time zone: n/a (CST, +0800)  #时区
     NTP enabled: yes  #是否开启NTP时间同步服务
NTP synchronized: yes  #是否开启NTP时间同步
 RTC in local TZ: yes  #主板时间是否和本地时间保持一致
      DST active: n/a

#设置东京时区
timedatectl set-timezone Asia/Tokyo

#查看
timedatectl
date -R

#调整回来
timedatectl set-timezone Asia/Shanghai

#将主板时间和你本地时间保持一致
timedatectl set-local-rtc 1

#关闭将主板时间和你本地时间保持一致
timedatectl set-local-rtc 0

#列出支持的时区
timedatectl list-timezones

```




