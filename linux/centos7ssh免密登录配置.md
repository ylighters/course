##centos7 ssh免密登录配置
以两个服务器为例
```
1. 公钥密钥生成，两个服务器上都执行
   ssh-keygen -t rsa -P '' -f ~/.ssh/id_rsa
2. 将公钥复制到另一个服务器中，注意另一个服务中要有文件夹 /root/sshpub/，否则复制报错。复制的文件可以根据服务器命名，但记得写入~/.ssh/authorized_keys时要使用对应的文件
   [root@localhost ~]# scp /root/.ssh/id_rsa.pub root@192.168.2.216:/root/sshpub/j1.pub
3. 将公钥写入~/.ssh/authorized_keys，注意写入公钥方式使用>>追加模式
   [root@localhost ~]# cat ~/sshpub/j1.pub >> ~/.ssh/authorized_keys
4. 只要保证另一个服务器有当前服务器的公钥并且写入了~/.ssh/authorized_keys中，那么当前服务器就可免密ssh登录到另一台服务器上

ssh需要授权chmod 777
```
