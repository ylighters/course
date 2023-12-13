# mysql语法

登录：
> mysql -uroot -p

显示所有数据库：
> show databases;

进入某库 

> use 数据库名称;

建立数据库：
> mysql> CREATE DATABASE 库名;

> mysql> CREATE DATABASE IF NOT EXISTS my_db default charset utf8 COLLATE utf8_general_ci;

执行某条sql
> source /usr/dx_check_grade.sql;

导出sql
> mysqldump -h localhost -u root -proot jndx_real > /home/jndxreal20190422_qh1.sql;

> mysqldump -h localhost -u root -pjnyz1@3. thzhjz > /home/thzhjz_20231211.sql;

查看当前使用的数据库 
> select database();

查看数据库使用端口 
> show variables  like 'port';

查看数据库的表信息 
> show tables;

查看数据库的所有用户信息
> select distinct concat('user: ''',user,'''@''',host,''';') as query from mysql.user;

查看数据库的最大连接数 
> show variables like '%max_connections%';

查看数据库当前连接数，并发数 
> show status like 'Threads%';

查看数据文件存放路径 
> show variables like '%datadir%';

刷新连接数
> mysqladmin  -u  root  -p  flush-hosts

开通数据库远程连接权限

> GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY 'mypassword' WITH GRANT OPTION;


flush privileges; #刷新一下
