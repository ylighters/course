# linux部署java环境
### 通用命令

查看启动服务：netstat -an | grep LI
tar zxvf 压缩包名称
vim    i:输入  esc:退出编辑    :wq  保存退出
打印tomcat ：# tail -f catalina.out
先查看后台进程pid：ps -aux
查询java服务：ps -ef|grep java

解决部署服务图片无法显示，该问题的解决方法：
第一次看到的是在大约392行的地方加
在Tomcat/bin/catalina.sh 中增加-Djava.awt.headless=true

虚拟机网络开启 vi /etc/sysconfig/network-scripts/ifcfg-eth0  root改为true
ifdown eth0关闭
  ifup eth0
压缩：
zip  -r  文件夹.zip  文件夹
解压：
unzip  文件夹.zip  -d  文件夹

### 安装命令
检测命令：echo $PATH  
ls /sbin|grep ifconfg
安装命令：yum search ifconfig
yum install net-tools.x86_64
yum install wget
yum install vim

### TOMCAT
- 自启动tomcat
启动，查看状态，重启，关闭 tomcat
　　systemctl start tomcat.service
　　systemctl status tomcat.service
　　systemctl restart tomcat.service
　　systemctl stop tomcat.service
- 下载tomcat压缩包
  直接ftp工具拷贝到数据库，源码复制到webapp下，启动停止服务在bin文件夹下执行sh start.sh 或者sh shutdown.sh命令
  
  
  
### MariaDB
 安装mariadb
  yum install mariadb-server mariadb 

centos7自带数据库MariaDB重启和修改密码
1：MariaDB和mysql差不多是mysql的一个分支，完全兼容mysql的命令。
2：centos 7 中自带MariaDB， 需要在centos中安装mysql的时候就需要多注意了。
3：启动 停止 重启 MariaDB
      systemctl start mariadb.service #启动MariaDB
      systemctl stop mariadb.service #停止MariaDB
      systemctl restart mariadb.service #重启MariaDB
      systemctl enable mariadb.service #设置开机启动
4：如果忘记了MariaDB root密码怎么办呢？
    1. KILL掉系统里的MySQL进程；
    2. 用以下命令启动MySQL，以不检查权限的方式启动；
    mysqld_safe -skip-grant-tables &
    或是
    修改/etc/my.cnf文件,在[mysqld]下添加 skip-grant-tables , 再启动mysql
    3. 然后用空密码方式使用root用户登录 MySQL；
    mysql -u root
    4. 修改root用户的密码；
    mysql> update mysql.user set password=PASSWORD('新密码') where User='root'
    mysql> flush privileges；
    mysql> quit
    5. 重新启动MySQL，就可以使用新密码登录了。
    6. 改完密码别忘记删除配置文件中的 skip-grant-tables
5：普通修改MariaDB的密码，则和上一步一样，不用修改配置文件，登录MySQL 就行
6：普通重启mysql和启动mysql
   启动： /usr/local/mysql/bin/mysqld_safe -user=mysql &
   停止：/usr/local/mysql/bin/mysqladmin -uroot -p shutdown
   
   
   
### JDK
https://www.cnblogs.com/sxdcgaq8080/p/7492426.html
1、安装jdk  
如果系统自带openjdk先卸载jdk再重新装
https://www.cnblogs.com/CuteNet/p/3947193.html
最好还是先卸载掉openjdk,在安装sun公司的jdk.
先查看 rpm -qa | grep java
显示如下信息：
    java-1.4.2-gcj-compat-1.4.2.0-40jpp.115
    java-1.6.0-openjdk-1.6.0.0-1.7.b09.el5
卸载：
    rpm -e --nodeps java-1.4.2-gcj-compat-1.4.2.0-40jpp.115
    rpm -e --nodeps java-1.6.0-openjdk-1.6.0.0-1.7.b09.el5
还有一些其他的命令
    rpm -qa | grep gcj
    rpm -qa | grep jdk
如果出现找不到openjdk source的话，那么还可以这样卸载
    yum -y remove java java-1.4.2-gcj-compat-1.4.2.0-40jpp.115
    yum -y remove java java-1.6.0-openjdk-1.6.0.0-1.7.b09.el5

mkdir /usr/java
下载jdk  到/usr/java
wget --no-check-certificate --no-cookies --header "Cookie: oraclelicense=accept-securebackup-cookie" https://download.oracle.com/otn-pub/java/jdk/8u191-b12/2787e4a523244c269598db4e85c51e0c/jdk-8u191-linux-x64.tar.gz
解压命令：tar zxvf 压缩包名称 （例如：tar zxvf jdk-8u152-linux-x64.tar.gz）
删除命令：rm -f 压缩包名称 （例如 rm -f jdk-8u152-linux-x64.tar.gz）
编辑命令：vi /etc/profile
export JAVA_HOME=/usr/java/jdk1.8.0_152 
export CLASSPATH=.:$JAVA_HOME/jre/lib/rt.jar:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar 
export PATH=$PATH:$JAVA_HOME/bin
生效命令：source /etc/profile
测试命令：java -version

### 防火墙firewall
查看firewall的状态
firewall-cmd --state
3、开启、重启、关闭、firewalld.service服务
 开启
service firewalld start
重启
service firewalld restart
关闭
service firewalld stop
4、查看防火墙规则
firewall-cmd --list-all 
5、查询、开放、关闭端口
查询端口是否开放
firewall-cmd --query-port=8080/tcp
开放80端口
firewall-cmd --permanent --add-port=80/tcp
移除端口
firewall-cmd --permanent --remove-port=8080/tcp
配置端口转发8080转到8000
firewall-cmd --add-forward-port=port=8000:proto=tcp:toport=8080 --permanent
重启防火墙(修改配置后要重启防火墙)
firewall-cmd --reload
参数解释
1、firwall-cmd：是Linux提供的操作firewall的一个工具；
2、--permanent：表示设置为持久；
3、--add-port：标识添加的端口；

1） 永久性生效，重启后不会复原

开启： chkconfig iptables on

关闭： chkconfig iptables off

2） 即时生效，重启后复原

开启： service iptables start

关闭： service iptables stop

正确关闭步骤：

service iptables stop 

chkconfig  iptables off 
