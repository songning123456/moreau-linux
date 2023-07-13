![](https://images.gitee.com/uploads/images/2021/0425/142340_58f7d41a_1479682.png)  

### 一. 应用层  
#### 模拟和百度进行通讯:  
1.建立连接(内核去做的)   
```shell
exec 8<> /dev/tcp/www.baidu.com/80
# 含义: /dev/tcp/www.baidu.com/80:与百度的80端口通过socket技术建立了tcp连接,用8去指向了这个连接,从连接中可以拿到输入流和输出流        
# 8<>  相当于是一个输入输出流的channel(通道),8就是文件描述符.      
# exec [-cl] [-a 名称] [命令 [参数 ...]] [重定向 ...]  这个用法会用指定命令去替换shell.只不过上面那句话中没有[命令]这个选项,只是添加了一个重定向（没有用任何给出的cmd覆盖自己,但是给出了重定向的定义语法增加了socket)   
# 可以通过cd /proc/$$/fd 中找到这个重定向,ll查看        
# $$：就是当前程序的进程 ,fd文件描述符. 可以echo $$：就是当前程序的进程号(这里一般是shell窗口的进程号).    
```
![](https://images.gitee.com/uploads/images/2021/0427/094257_36dffef1_1479682.png)   
shell(bash)就是一个守护进程,当用ssh登录的时候fork出一个bash的进程,使用`exec ls -l`替换了这个进程  
![](https://images.gitee.com/uploads/images/2021/0427/100928_1e80d426_1479682.png)  
比如`exec ls -l`,用`ls -l`命令当前`shell`,因为`ls -l`就是启动->打印->退出,替换掉`shell`之后,就会造成程序退出了    
![](https://images.gitee.com/uploads/images/2021/0427/100259_ba664f02_1479682.png)  


2.http通讯(用户去做)   
```shell
echo -e "GET / HTTP/1.0\n" 1>& 8　　
#/表示主页,-e能够让\n得到转义,1表示echo的标准输出, 后面是重定向到前面定义的8中. 注意这里, > 到文件;    >& 到文件描述符.
```

3.读取百度回复的消息(用户去做的)   
```shell
cat 0<& 8

#/proc:该目录记录了操作系统中所有的进程,$$：就是当前程序的进程 
cd /proc/$$ 
#fd:文件描述符.
cd /proc/$$/fd 
#查看
ll
```
![](https://images.gitee.com/uploads/images/2021/0427/092940_e0b3e51c_1479682.png)   
文件描述符:8指向了一个socket,可以通过fd:8拿到socket输入输出流    


我们可以看一下,redis进程下也有输入输出,文件描述符的信息   
![](https://images.gitee.com/uploads/images/2021/0427/094617_bf9272c2_1479682.png)   

### 二.传输控制层(TCP/IP)  
![](https://images.gitee.com/uploads/images/2021/0423/170052_b2633928_1479682.png)   


1.TCP:  
面向连接, 可靠的传输.   

为什么握手要三次?   
socket的IO通讯是双向的,两对syn+ack,分别表示客户端确认自己发送的消息服务端能够收到,以及服务端发送的客服端能够收到.    

三次握手之后,双方开辟了资源.    

2.linux中查看网络状态   
![](https://images.gitee.com/uploads/images/2021/0423/211531_1b367465_1479682.png)   

ssh:secure shell,安全外壳协议,应用层协议.ftp,telnet本质上是不安全的.   

这里的Local Address就是代表的你说连接到的linux服务器,而Foreign Address表示对端地址.  
上面的是每个socket对应一个进程,而多个socket对应一个进程就是 多路复用器 (select, poll, epoll).    

3.抓取访问百度过程(通过`curl www.baidu.com`)的数据包,验证3次握手和四次分手.    
![](https://images.gitee.com/uploads/images/2021/0423/211717_a594a0c7_1479682.png)    
其中[S]代表的是SYN,[.]代表的是ACK.[F]代表FIN.   

中间的部分表示数据传输,一开始是1440字节,然后客户端给一个确认;接着是1341字节,然后客户端给一个确认.(因为数据包的大小是有限制的,比如图中说的[mss 1460])    

### 三.网络层和数据链路层   
![](https://images.gitee.com/uploads/images/2021/0425/125053_2f7da513_1479682.png)   
![](https://images.gitee.com/uploads/images/2021/0423/212003_5caf6132_1479682.png)   

#### IP地址 & 子网掩码得到网络号  
![](https://images.gitee.com/uploads/images/2021/0423/212112_ad4c28c4_1479682.png)   

理解路由表中表示的含义:     
如果要访问百度的ip,拿百度的ip去和路由表中的子网掩码进行按位与操作, 得到的结果跟路由表中的Destination列的值去进行匹配.这里只有和0.0.0.0去按位与得到0.0.0.0可以与第一条匹配上,这个时候就去找对应的Gateway,这里是192.168.121.2.这是个路由器网关,路由器上有自己的路由表,也有它的下一跳地址,就这么下一跳,下一跳...直到找到百度的地址....     

而访问本局域网中的ip(192.168.121.11),和255.255.255.0按位与之后,得到的是网络号,它对应的网关是0.0.0.0,表示不需要通过网关转发(不需要下一跳,局域网直连).      

![](https://images.gitee.com/uploads/images/2021/0423/212255_3516ce73_1479682.png)   


#### 在传输过程中,数据包中究竟需要存储目标ip的地址还是下一跳的地址呢?   
实际上存储的是目标ip,只不过在数据包外层要封装下一跳的mac地址   
![](https://images.gitee.com/uploads/images/2021/0423/212358_f510ae78_1479682.png)   

#### 查看arp表中的内容:  
```shell
arp -a
```
![](https://images.gitee.com/uploads/images/2021/0423/212433_d83a2800_1479682.png)   

#### 删除网关的mac地址,再去请求外网:   
```shell
#首先在一个shell窗口抓包
tcpdump -nn -i ens33 port 80 or arp

#-X:可以看到详细的抓包信息
tcpdump -nn -X -i ens33 port 80 or arp
```

```shell
#另外一个shell窗口删除mac地址,在请求baidu
arp -d 192.168.121.2 && curl www.baidu.com
```

抓包结果:  
![](https://images.gitee.com/uploads/images/2021/0423/212708_9b47a260_1479682.png)  
发现会先进行arp请求,找到网关的mac地址,再去进行三次握手和四次分手的过程.   

#### 手动添加网关,理解网关的含义  
假如原先主机A(192.168.121.4)和主机B(192.168.121.1)是同一个网段,此时它们两个是可以互相访问的.现在在主机B上添加一个ip地址ipB2,比如说是192.168.88.88(通过`ifconfig ens33:0 192.168.88.88 netmask 255.255.255.0 up`),此时主机A是访问不到该地址的.因为主机A的路由表中并没有到此地址的路由.如下图所示,是主机A的路由表,在`ping 192.168.88.88`时,会对子网掩码进行与运算,匹配到192.168.121.2(下一跳地址),但是121.2并不能够找到88.88,所以无法去访问.      

![](https://images.gitee.com/uploads/images/2021/0423/212834_05b24d8d_1479682.png)   

在主机A中可以通过人为添加路由的方式,来找到88.88地址.方式如下:   
![](https://images.gitee.com/uploads/images/2021/0423/212913_7f9667b8_1479682.png)   

告诉主机A,如果要访问88.88可以通过121.1.   

### 补充  
#### scoket其实就是一个(fd)文件描述符指向的一个四元组    
只要满足ip:port+ip:port的唯一性,这个连接就能被创建出来   
![](https://images.gitee.com/uploads/images/2021/0427/102116_927ffb27_1479682.png)  

#### 程序和内核间调用
![](https://images.gitee.com/uploads/images/2021/0427/102238_584bcd06_1479682.png)  


### LVS下TCP通信和DDos简单原理  
![](https://images.gitee.com/uploads/images/2021/0427/102439_7afd4dad_1479682.png)   




















