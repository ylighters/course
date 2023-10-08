##centos7mysql自动备份

###1、创建sh备份文件
> /bin/sh
/usr/local/mysql3308/bin/mysqldump  -S /data/mysql3308/mysql.sock -P 3308 -uroot -pRcjtj@Rxpt@1234$ rcrxgl > /data/mysqldata/mysql_$(date "+%Y%m%d_%H:%M:%S").sql;
find /data/mysqldata -mtime +7 -name "mysqlTable_*.sql" -exec rm -rf {} \;

>暂时不管用/usr/local/mysql3308/bin/mysqldump  --defaults-extra-file=/usr/local/mysql3308/my.cnf  --basedir=/usr/local/mysql3308  --datadir=/data/mysql3308  rcrxgl > /data/mysqldata/mysql_$(date "+%Y%m%d_%H:%M:%S").sql;

###2、给sh文件授权
chmod +x backupsql.sh

###3、增加定时任务
crontab -e
编写代码

* * * * *  每1分钟执行一次
    
15,30,45 * * * *         每小时的第15、30、45分执行

15,30 10-11 * * *         在上午10点到11点的第15和第30分钟执行

0 */2 * * *                 每两个小时执行一次
0 */3 * * *
30 2 * * * /usr/sbin/mysql_db_backup.sh

3.1 实例1：
每1分钟执行一次myCommand

* * * * * myCommand
          1
          3.2 实例2：
          每小时的第3和第15分钟执行

3,15 * * * * myCommand
1
3.3 实例3：
在上午8点到11点的第3和第15分钟执行

3,15 8-11 * * * myCommand
1
3.4 实例4：
每隔两天的上午8点到11点的第3和第15分钟执行

3,15 8-11 */2  *  * myCommand
1
3.5 实例5：
每周一上午8点到11点的第3和第15分钟执行

3,15 8-11 * * 1 myCommand
1
3.5实例6：每晚的21:30重启smb
30 21 * * * /etc/init.d/smb restart
1
3.5实例7：每月1、10、22日的4 : 45重启smb
45 4 1,10,22 * * /etc/init.d/smb restart
