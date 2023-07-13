### SSH
ssh:secure shell,protocol,22/tcp,安全的远程登录  
具体的软件实现:  
OpenSSH:ssh协议的开源实现,CentOS默认安装   
相关包:  
openssh openssh-clients  
openssh-server  

工具:  
基于c/s架构  
Client:ssh,scp,sftp,slogin  
Windows客户端:  
xshell,putty,securecrt,sshsecureshellclient  
Server:sshd  

对称加密:  
![](https://images.gitee.com/uploads/images/2020/0602/154217_787df301_1479682.png)  

非对称加密  
![](https://images.gitee.com/uploads/images/2020/0602/154837_7eb5b4f5_1479682.png)  

基于密钥的登录方式  
1.首先在客户端生成一对密钥 (ssh-keygen)  
2.并将客户端的公钥ssh-copy-id拷贝到服务器  
3.当客户端再次发送一个连接请求,包括ip、用户名  
4.服务端得到客户端的请求后,会到authorized_keys中查找,如果有响应的ip和用户,就会随机生成一个字符串,例如:acdf  
5.服务端将使用客户端拷贝过来的公钥进行加密,然后发送给客户端  
6.得到服务端发送的消息后,客户端会使用私钥进行解密,然后将解密后的字符串发送给服务端  
7.服务端接受到客户端发来的字符串后,跟之前的字符串进行对比,如果一致,就允许免密码登录  

### xshell配置ssh免密登录  
ssh登录提供两种认证方式：口令(密码)认证方式和密钥认证方式。其中口令(密码)认证方式是我们最常用的一种，这里介绍密钥认证方式登录到linux的方法。  
使用密钥登录分为3步：  
1、生成密钥（公钥与私钥）；  
2、放置公钥(Public Key)到服务器~/.ssh/authorized_key文件中；  
3、配置ssh客户端使用密钥登录。  

一、生成密钥公钥（Public key）与私钥(Private Key)  
打开xshell中的工具下的新建用户密钥生成导向  
![](https://images.gitee.com/uploads/images/2020/0602/171008_cd5ec038_1479682.png)  
![](https://images.gitee.com/uploads/images/2020/0602/171019_d26b3d1a_1479682.png)  
在“密钥类型”项选择“RSA”公钥加密算法，“密钥长度”选择为“2048”位密钥长度  
![](https://images.gitee.com/uploads/images/2020/0602/171034_53ea5fb6_1479682.png)  
填写密钥名称，密码（密码等下会用到），确认密码，下一步，  
![](https://images.gitee.com/uploads/images/2020/0602/171053_116ae7e8_1479682.png)  
复制公钥里面的内容  
![](https://images.gitee.com/uploads/images/2020/0602/171109_dc08e662_1479682.png)  
点击“保存文件为”按钮，将公钥(Public key)保存到磁盘，文件名为“id_rsa_2048.pub”，备用。  
![](https://images.gitee.com/uploads/images/2020/0602/171125_50b694e1_1479682.png)  

二、放置公钥(Public Key)到服务器  
1、方法一（我使用的是阿里云服务器，所以我直接登陆到控制台）  
打开控制台云服务里面的产品与服务，点击创建密钥对  
![](https://images.gitee.com/uploads/images/2020/0602/171156_4cdb807a_1479682.png)  
选择导入已有的密钥对，填写密钥对名称，把刚才复制的公钥内容粘贴进来，确定  
![](https://images.gitee.com/uploads/images/2020/0602/171210_5af39560_1479682.png)  
把密钥添加到主机中  
![](https://images.gitee.com/uploads/images/2020/0602/171225_5f8256fb_1479682.png)  
![](https://images.gitee.com/uploads/images/2020/0602/171237_f57ef63b_1479682.png)  
重启服务器  
![](https://images.gitee.com/uploads/images/2020/0602/171254_4cf5b7cb_1479682.png)  

2、方法二  
登录到服务器，进入到“/root/.ssh/”目录，运行rz命令(如果没有rz命令，运行yum install lrzsz安装)，将key.pub发送到服务器，然后运行如下命令，将公钥(Public Key)导入到“authorized_keys”文件：  

```shell
[root@localhost ~]# cd /root/.ssh/
[root@localhost .ssh]# rz
rz waiting to receive.
[root@localhost .ssh]# cat me.pub >> authorized_keys
[root@localhost .ssh]# chmod 600 authorized_keys
[root@localhost .ssh]# ls
authorized_keys known_hosts me.pub
[root@localhost .ssh]# cat authorized_keys
ssh-rsa AAAAB3NzaC1yc2EAAAABIwAAAQEA4VCOWFrARc1m3MfEAL50v2Z2siavO3Ijpr/LZ00EZah8EjfZhqjAc/agkljyXmNGpVDpRdtlYco8h3P5vegXOEgKcX74fDYm0vNdVABVD1XSD8ElNyLTCCNk7rZJbi3htJox3Q1n0vnMmB5d20d9occkAx4Ac94RWNS33EC5CszNTMgAIn+uZl0FlQklS1oSyWFahSTWyA6b33qG7Y5E4b6J/caObnPx6EgtBrgi97gXJHZWyYlGrpWmUuhPqs5XToRB08CVxAyzewtq1MXv0p+Po4L1pbHLRf+TSoZ5RSBZZjY4/JMAzdXHNtnAyO0j0VNGbBXKUcNSAHZ
```
![](https://images.gitee.com/uploads/images/2020/0602/171343_e94ab7f8_1479682.png)  

三、配置ssh客户端使用密钥登录。  
打开xshell，填写主机地址，选择22端口  

![](https://images.gitee.com/uploads/images/2020/0602/171406_b4fa01ca_1479682.png)  

方法选择public key，用户名一般为root,选择用户密钥，填写刚才设置的密码  

![](https://images.gitee.com/uploads/images/2020/0602/171423_820d3915_1479682.png)  

由图可见，登陆成功  

![](https://images.gitee.com/uploads/images/2020/0602/171438_1a00e4c6_1479682.png)  






