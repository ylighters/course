##minio
>MinIO 是一款高性能、分布式的对象存储系统. 它是一款软件产品, 可以100%的运行在标准硬件。即X86等低成本机器也能够很好的运行MinIO。
http://www.minio.org.cn/

+ 查看minio进程
ps -ef|grep minio
+ kill 进程数

###下载mc命令
cd /opt
wget http://dl.minio.org.cn/client/mc/release/linux-amd64/mc

ls

给mc文件赋权限
chmod u+x mc

./mc ls

./mc config

./mc config host

./mc config host ls

添加 MinIO服务
./mc config host add local http://10.2.17.10:9000 minioadmin@sdyz minioadmin@sdyz

./mc ls local upload

./mc ls local/upload | more

./mc ls local/upload > a.txt

./mc cp -r local/upload /data/minio_bak2022/

cd /data

zip -r minio_bak2022.zip minio_bak2022/


./mc ls local

./mc cp local/upload /data/minio_back/

./mc cp -R local/upload /data/minio_back/

 ./mc cp -r local/upload /data/minio_back/

 ./mc cp -r local/uploadczyey /data/minio_back/
