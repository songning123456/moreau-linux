查看并卸载已安装的JDK  
```shell
rpm -qa|grep java

#卸载
#rpm -e --nodeps xxxx
```
图中可以看到系统自带的OpenJDK的版本等相关信息。下面我们进行卸载，使用 rpm -e --nodeps命令进行卸载，如下图所示：   
![](https://images.gitee.com/uploads/images/2021/0301/112243_ee3b8249_1479682.png)  


```shell
tar -zxvf jdk-8u251-linux-x64.tar.gz 

mkdir /usr/local/jdk -p

mv jdk1.8.0_251 /usr/local/jdk

#环境变量
vim /etc/profile

export JAVA_HOME=/usr/local/jdk
export JRE_HOME=$JAVA_HOME/jre
export CLASSPATH=.:$JAVA_HOME/jre/lib/rt.jar:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
export PATH=$PATH:$JAVA_HOME/bin:$JRE_HOME/bin


#使环境变量生效
source /etc/profile



#测试
java
javac
java -version
```