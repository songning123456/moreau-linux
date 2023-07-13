### 方法一：  

在/etc/profile文件中添加变量【对所有用户生效(永久的)】  

用VI在文件/etc/profile文件中增加变量，该变量将会对Linux下所有用户有效，并且是“永久的”。  

要让刚才的修改马上生效，需要执行以下代码  
```shell
source /etc/profile  
 ```

`vim /etc/profile`,加入文件的末尾:  
```shell

#设置环境变量
export ASPNETCORE_ENVIRONMENT=Production

#设置可执行文件路径(PATH)
#JDK1.8
export JAVA_HOME=/usr/jdk1.8.0_211
export PATH=$PATH:$JAVA_HOME/bin
 
#mongodb4.0.9
export MONGODB_HOME=/usr/local/mongodb4.0.9  
export PATH=$PATH:$MONGODB_HOME/bin
```

```shell
#打印PATH
echo $PATH
```

最后记得`source /etc/profile`使刚才的配置生效  



### 方法二： 

在用户目录下的.bash_profile文件中增加变量【对单一用户生效(永久的)】  

用VI在用户目录下的.bash_profile文件中增加变量，改变量仅会对当前用户有效，并且是“永久的”。  

要让刚才的修改马上生效，需要在用户目录下执行以下代码  
```shell
source .bash_profile  
```

### 方法三：  

直接运行export命令定义变量【只对当前shell(BASH)有效(临时的)】  

在shell的命令行下直接使用[export变量名=变量值]定义变量，该变量只在当前的shell(BASH)或其子shell(BASH)下是有效的，shell关闭了，变量也就失效了，再打开新shell时就没有这个变量，需要使用的话还需要重新定义。  

例如：`export PATH=/usr/local/webserver/php/bin:$PATH`  

```shell
#临时的环境变量
export asp=123
echo $asp
#列出所有环境变量
export
```

