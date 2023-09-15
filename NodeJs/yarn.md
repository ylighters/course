###一、修改npm的包的全局安装和缓存路径
- window + r -> cmd （路径为你想要更改的路径）
 - npm config set prefix "D:\Develop\npm\node_gobal"
- npm config set cache "D:\Develop\npm\node_cache"

查看是否更改成功

npm config ls

测试

npm install express -g


###二、yarn的全局安装和配置
yarn全局安装

// 前面已经修改了npm的包全局的安装路径，yarn会自动安装到已经更改的路径中

npm install yarn -g


所以，只需修改yarn的包的全局安装路径


更改yarn的包全局命令

yarn config set global-folder "路径"
yarn config set global-folder "D://yarn"

yarn config set cache-folder "D://yarn"


查看是否更改成功

yarn global dir

测试

yarn add express -g
