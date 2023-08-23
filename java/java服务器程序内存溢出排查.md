# java服务器程序内存溢出排查

### 一、排查内存高应用
top、top -c

### 二、查看java端口
ps -ef|grep java

### 三、排查内存高应用
top -H -p 进程号

### 四、下载服务器对应进程的dump文件
jmap -dump:live,format=b,file=/data/mydump.hprof 进程号 (只dump存活的日志)

### 五、用jprofiler工具进行分析
用jprofiler工具进行分析，下面是分析步骤

https://www.ej-technologies.com/download/jprofiler/files

https://blog.csdn.net/JavaBlackHole/article/details/124040556
