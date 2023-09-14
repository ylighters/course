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
./mc config host add local http://10.2.17.9:9000 minioadmin@sdyz minioadmin@sdyz

./mc ls local upload

./mc ls local/upload | more

备份小学文件

./mc ls local/upload > a.txt

./mc cp -r local/upload /data/minio_bak2023/

备份幼儿园初中文件
./mc ls local/uploadczyey > a.txt
./mc cp -r local/uploadczyey /data/minio_bak2023_czyey/

cd /data

zip -r minio_bak2022.zip minio_bak2022/


./mc ls local

./mc cp local/upload /data/minio_back/

./mc cp -R local/upload /data/minio_back/

 ./mc cp -r local/upload /data/minio_back/

 ./mc cp -r local/uploadczyey /data/minio_back/

###删除服务器静态资源
+ 使用以下命令列出所有桶（bucket）

  ./mc ls local
  
  ./mc ls local/upload | more

+ 然后，使用以下命令删除所有桶：

  >mc rm --recursive local/upload --force all

+ 接下来，使用以下命令删除指定桶中的所有对象
  >mc rm --recursive mybucket1/* --force -p mybucket1/path/to/files/and/folders/to/be/deleted/* --confirm=true --no-preemption --skip-trash &> /dev/null && echo "Done" || echo "Failed to delete objects from bucket."
  
  其中，mybucket1是要清除资源的桶的名称，path/to/files/and/folders/to/be/deleted/* 是要删除的文件和文件夹的路径。请注意，这个命令会递归地删除指定路径下的所有文件和文件夹。如果您只想删除特定类型的文件或文件夹，请在路径中指定它们的类型。例如：--include="*.jpg"表示只删除所有JPEG格式的图片文件。此外，--confirm=true参数会在删除对象之前要求确认。如果您不想提示确认，请将其设置为false。最后，--skip-trash参数会将删除的对象移动到回收站而不是永久删除它们。如果您想永久删除这些对象，请省略此参数。

###注意
mc rm --recursive命令用于递归删除指定路径下的所有文件和文件夹。例如，如果您想删除一个目录及其所有子目录和文件，可以使用以下命令：
mc rm --recursive myfolder/*
其中，myfolder是要删除的目录的名称。请注意，这个命令会递归地删除指定路径下的所有文件和文件夹。如果您只想删除特定类型的文件或文件夹，请在路径中指定它们的类型。例如：--include="*.jpg"表示只删除所有JPEG格式的图片文件。
