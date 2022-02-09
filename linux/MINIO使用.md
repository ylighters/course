##minio
>MinIO 是一款高性能、分布式的对象存储系统. 它是一款软件产品, 可以100%的运行在标准硬件。即X86等低成本机器也能够很好的运行MinIO。
http://www.minio.org.cn/

+ 查看minio进程
ps -ef|grep minio
+ kill 进程数

安装部署
官方文档: http://docs.minio.org.cn/docs
下载安装
网站地址：https://min.io/download
下载地址：https://dl.min.io/server/minio/release/linux-amd64/minio
安装和启动：
# 创建data目录
mkdir /minio/data
# 添加可执行权限
chmod +x minio
# 设置登录minio的 access key,可以在shell中设置
export MINIO_ACCESS_KEY=minioadmin
# 设置登录minio的 secret key,可以在shell中设置
export MINIO_SECRET_KEY=minioadmin
# 启动 minio
./minio server /minio/data
# 修改端口号需要添加 --address=0.0.0.0:9006 参数
./minio server /minio/data --address=0.0.0.0:9006 >> /minio/info.log 2> error.log

安装后使用浏览器访问 http://ip:9000
设置永久访问策略：
# mc 下载地址：http://dl.minio.org.cn/client/mc/release/linux-amd64/mc
# 添加云服务
mc config host add minio http://ip:9000 minioadmin minioadmin
# 设置永久访问
mc policy set download minio/{bucketName}
