### 一：VMware虚拟机克隆CentOS7  
这里介绍下VMware下有三种网络连接模式  

birdged（桥接）  
桥接网络是指本地物理网卡和虚拟网卡通过VMnet0虚拟交换机进行桥接，物理网卡和虚拟网卡在拓扑图上处于同等地位，那么物理网卡和虚拟网卡就相当于处于同一个网段，虚拟交换机就相当于一台现实网络中的交换机,所以两个网卡的IP地址也要设置为同一网段。  

NAT(网络地址转换)  
NAT模式中，就是让虚拟机借助NAT(网络地址转换)功能，通过宿主机器所在的网络来访问公网。 NAT模式中，虚拟机的网卡和物理网卡的网络，不在同一个网络，虚拟机的网卡，是在VMware提供的一个虚拟网络。  

Host-only(仅主机)  
在Host-Only模式下，虚拟网络是一个全封闭的网络，它唯一能够访问的就是主机。其实Host-Only网络和NAT网络很相似，不同的地方就是Host-Only网络没有NAT服务，所以虚拟网络不能连接到Internet。主机和虚拟机之间的通信是通过VMware Network Adepter VMnet1虚拟网卡来实现的。  

主要讨论桥接和NAT模式  
NAT和桥接的比较:  
(1) NAT模式和桥接模式虚拟机都可以上外网。  

(2) 由于NAT的网络在VMware提供的一个虚拟网络里，所以局域网其他主机是无法访问虚拟机的，而宿主机可以访问虚拟机，虚拟机可以访问局域网的所有主机，因为真实的局域网相对于NAT的虚拟网络，就是NAT的虚拟网络的外网，不懂的人可以查查NAT的相关知识。  

(3) 桥接模式下，多个虚拟机之间可以互相访问；NAT模式下，多个虚拟机之间也可以相互访问。 
 
使用NAT模式  
下面进行操作  
1.准备，装好一台镜像安装好的虚拟机，如下图中的CentOS7-S-1：  
![](https://images.gitee.com/uploads/images/2020/1014/120228_7de36e67_1479682.png)   

2.在左边的CentOS7-S-1上面右键–>管理–>克隆，出现如下页面：  
![](https://images.gitee.com/uploads/images/2020/1014/120244_31529545_1479682.png)  

3.点击下一步，如下图：  
![](https://images.gitee.com/uploads/images/2020/1014/120258_d0b89a83_1479682.png)  

对应自己的需求选择，如下图  
![](https://images.gitee.com/uploads/images/2020/1014/120310_3d7538e3_1479682.png)  

在这里，我选择的完整克隆，点击下一步，如下图：  
![](https://images.gitee.com/uploads/images/2020/1014/120323_d4b13c65_1479682.png)  
最后点击完成就创建好了  

设置克隆的CentOS7-S-xx的静态Ip  
1.在新创建的虚拟机上进行如下操作，如下图：  
![](https://images.gitee.com/uploads/images/2020/1014/120356_e940f2cf_1479682.png)  

第一步为鼠标右击，主要目的是第五步，避免克隆导致mac地址重复，产生冲突，完成后进行下一步  
2.如下图，以此进行右键编辑–>选择虚拟网络编辑器–>,弹开的页面如下图，以此进行操作：  
![](https://images.gitee.com/uploads/images/2020/1014/120417_a22b8252_1479682.png)  
记住这里的网关地址  

为系统设置一个静态ip，方便日后使用远程连接工具  
`vim /etc/sysconfig/network-scripts/ifcfg-ens33`
![](https://images.gitee.com/uploads/images/2020/1014/120439_c3f9c609_1479682.png)  

重启网络服务 CentOS7中使用如下命令 systemctl restart network.service  

验证网络配置是否成功 ping 一下 www.baidu.com 或者 www.qq.com  

ping通了，就说明NAT模式下的网络配置成功了！  

完结！  

 