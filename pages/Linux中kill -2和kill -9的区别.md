在Linux中用Kill-2和Kill-9都能够结束进程,他们之间的区别为:  

Kill-2:功能类似于Ctrl+C是程序在结束之前,能够保存相关数据,然后再退出。  
Kill-9:直接强制结束程序。  
在用nohup挂起程序时,当想要结束这个程序,最好用kill-2。能够模拟在控制台结束程序的功能。  
