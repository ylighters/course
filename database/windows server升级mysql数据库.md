# windows server升级mysql数据库

### 1、下载最新mysql安装包
[mysql安装包](https://dev.mysql.com/downloads/mysql/4.1.html)

### 2、停止mysql

### 3、解压到对应路径 (mysql-5.7.43-winx64.zip)

### 4、备份旧数据库
![img.png](../image/img.png)

### 5、将旧版mysql 下的data文件和my.ini文件copy至新版mysql路径下

### 6、进入到旧版mysql文件夹bin目录下面执行
查看服务里面的名称替换 remove后面的名称

` mysqld --remove MySQL ` 

### 7、进入到新版mysql的bin文件里面执行mysql安装
` mysqld --install mysql ` 

### 8、启动Mysql服务

net start mysql

### 9、升级mysql：

mysql_upgrade -uroot -p

### 10、关闭、重启Mysql

net stop mysql
net start mysql

### 11、验证Mysql版本

select version(); 



### 参考链接
https://fireman.blog.csdn.net/article/details/107046152?spm=1001.2101.3001.6650.2&utm_medium=distribute.pc_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-2-107046152-blog-131681532.235%5Ev38%5Epc_relevant_sort_base2&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-2-107046152-blog-131681532.235%5Ev38%5Epc_relevant_sort_base2&utm_relevant_index=5

https://blog.csdn.net/solly793755670/article/details/131681532
