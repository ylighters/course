# java部署

## 一、启动命令

### 1、输入日志并指定对应配置文件
+ 这样启动会将日志输出到指定文件中，打包时不用再改配置
+ java  -Xms512m -Xmx1024m -jar ruoyi-admini.jar -Dfile.encoding=UTF-8 --spring.profiles.active=prod >/opt/java/logs/taiwin-medical-care-api.log 2>&1 &
+ java -Xms2048m -Xmx4096m -Dfile.encoding=UTF-8 -jar ruoyi-admin.jar -Ddruid.mysql.usePingMethod=false >catalina.out 2>&1 &
### 2、正常
+ java -Xms2048m -Xmx4096m -Dfile.encoding=UTF-8 -jar ruoyi-admin.jar --spring.profiles.active=prod