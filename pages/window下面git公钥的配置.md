github是一个基于git的代码托管平台，付费用户可以建私人仓库，我们一般的免费用户只能使用公共仓库，也就是代码要公开。   
　　由于本地Git仓库和GitHub仓库之间的传输是通过SSH加密的，所以必须要让github仓库认证你SSH key，在此之前，必须要生成SSH key。   
　　 
　　以下给出ｗindows中git bash关于ssh key的创建与配置。    
　　 
步骤1   
　　创建SSH Key。在windows下查看【c盘->用户->自己的用户名->.ssh】下是否有id_rsa、id_rsa.pub文件，如果没有需要手动生成。   
　　打开git bash，在控制台中输入以下命令。  

```shell
ssh-keygen -t rsa -C "your_email@xx.com"
```

![](https://images.gitee.com/uploads/images/2020/1204/111143_29659a4d_1479682.png)    

后面的your_email@xx.com改为你在github上注册的邮箱，之后会要求确认路径和输入密码，我们这使用默认的一路回车就行。  

![](https://images.gitee.com/uploads/images/2020/1204/111210_34025c90_1479682.png)  

步骤2   
　　先打开id_rsa.pub，文本编辑器如Notepad++等都可以，注意一定要按照打开时的源格式【包括开头”ssh..至.xx.com”】复制里面的key；接着登录github。打开Personal settings->SSH and GPG keys，   

![](https://images.gitee.com/uploads/images/2020/1204/111249_f328e503_1479682.png)  

点击右上角 New SSH key，把复制的id_rsa.pub里面的key按源格式粘贴到 key输入框中，再为当前的key起一个title来区分每个key。  

步骤３   
　　为了验证是否成功，在git bash下输入：  
```shell
ssh -T git@github.com
```
如果是第一次的会提示是否continue，输入yes回车再输入密码后就会看到：You’ve successfully authenticated, but GitHub does not provide shell access 。这就表示已成功连上github。 
　　如果出现问题，可以通过以下指令找到哪一步出问题  
```shell
ssh -v root@IP地址(github的为192.30.252.129)
```
