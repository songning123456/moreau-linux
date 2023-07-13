### GitLab依赖安装

```shell
sudo yum install -y curl policycoreutils-python openssh-server
sudo systemctl enable sshd
sudo systemctl start sshd
sudo firewall-cmd --permanent --add-service=http
sudo systemctl reload firewalld
```

### Postfix安装和设置默认开机自启动和启动
```shell
sudo yum install postfix
sudo systemctl enable postfix
sudo systemctl start postfix
```

### gitlab-ce下载地址
https://packages.gitlab.com/gitlab/gitlab-ce

### 安装(最新版本太大了,找了个300mb+的版本,而且起码要给虚拟机分配6g内存)
```shell
rpm -ivh gitlab-ce-10.2.3-ce.0.el7.x86_64.rpm
```

### 修改配置文件
```shell
vim /etc/gitlab/gitlab.rb
```
在配置文件里将`external_url 'http://gitlab.example.com'` 修改为你的ip或者域名地址 `external_url 'http://192.168.147.139'` 

配置完后执行`gitlab-ctl reconfigure`让你修改的配置生效,gitlab会重启它的服务

### gitlab汉化包下载
https://gitlab.com/xhang

### gitlab汉化

```shell
# 汉化前先关闭gitlab服务
gitlab-ctl stop

# 解压:
tar -xzvf gitlab-patch-zh.tat.gz

# 查看汉化补丁版本
cat /root/gitlab/VERSION

# 切换到 gitlab 汉化包所在的目录
cd /root/gitlab

# 比较汉化标签和原标签，导出 patch 用的 diff 文件到/root 下
git diff v10.2.3 v10.2.3-zh > ../10.2.3-zh.diff

# 将 10.2.3-zh.diff 作为补丁更新到 gitlab 中
patch -d /opt/gitlab/embedded/service/gitlab-rails -p1 < /root/10.2.3-zh.diff

# 重启服务，等 1 分钟，再去访问 web 页面。访问太快会显示 502 错误
gitlab-ctl restart
```


### 默认端口修改
```shell
由于默认gitlab使用80端口，所以，我们需要修改gitlab的默认端口

修改 /etc/gitlab/gitlab.rb 文件如下，然后执行重新配置，重启命令后完成。
gitlab-ctl reconfigure  //让配置生效，重新执行此命令时间也比较长
gitlab-ctl restart
端口要用9090以后的最好，否则不生效
默认情况下：unicorn会占用8080端口，nginx会占用80端口。改完之后，就可以通过nginx[‘listen_port’] 在浏览器进行访问了。
内部8080端口也不再被占用了。
```

![](https://images.gitee.com/uploads/images/2020/0922/111147_47c4b4ce_1479682.png)  

这样修改了默认端口80后，在idea中获取代码时，要在路径后面加上修改的端口号才可以访问到代码  
以上是按照gitlab的全过程。  


我的改法:  
![](https://images.gitee.com/uploads/images/2020/0922/111459_99e9b59c_1479682.png)  




