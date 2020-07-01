2020/6/30
docker 环境尾巴处理   为后期练习集群打基础
先用docker搭建一套可运行的php环境
在部署larvel-shop时卡壳
依赖包Elasticsearch，需要安装Elasticsearch应用，对应需要java环境，由于网速、以及gcc老旧以及各种不兼容问题环境未能部署。

jdk安装(java)

rpm -ivh https://download.oracle.com/otn-pub/java/jdk/14.0.1+7/664493ef4a6946b186ff29eb326336a2/jdk-14.0.1_linux-x64_bin.rpm?AuthParam=1593534825_b07d4b42440ff9b6860bdc747818170f
vi /etc/profile
export JAVA_HOME=/usr/java/jdk-14.0.1
export JRE_HOME=${JAVA_HOME}/jre
export CLASSPATH=.:${JAVA_HOME}/lib:${JRE_HOME}/lib
export PATH=${JAVA_HOME}/bin:$PATH

source /etc/profile
java -version


学生机编译安装nodejs 4个小时，到现在还没有编译完，睡觉。

2020/7/1
编译失败，直接下载node二进制文件，卸载原nodejs，加入软连接。

cd /usr/local
wget https://nodejs.org/dist/v12.18.2/node-v12.18.2-linux-x64.tar.xz
tar -xvJf  node-v12.18.2-linux-x64.tar.xz 
ln -s /usr/local/node-v12.18.2-linux-x64/bin/node /usr/bin/node
ln -s /usr/local/node-v12.18.2-linux-x64/lib/node_modules/npm/bin/npm-cli.js /usr/bin/npm

安装Elasticsearch，学生机内存太小，elasticsearch.service无法启动


切换到主服务器，使用docker安装elasticsearch(版本为7.8.0)容器
docker pull elasticsearch:7.8.0
docker run -d --name elasticsearch --net somenetwork -p 9200:9200 -p 9300:9300 -e "discovery.type=single-node" elasticsearch:7.8.0
进入容器,安装插件
docker exec -i -t elasticsearch bash
elasticsearch-plugin install https://github.com/medcl/elasticsearch-analysis-ik/releases/download/v7.8.0/elasticsearch-analysis-ik-7.8.0.zip
重启容器
docker restart elasticsearch

创建自定义网络
docker network create somenetwork
docker network ls 查看网络
docker inspect somenetwork 网络详情
将各容器加入同一网络
docker network connect somenetwork mysql-master
docker network connect somenetwork php-fpm-master
docker network connect somenetwork nginx-master
docker network connect somenetwork elasticsearch

拉取laravel-shop-advance项目
切换分支
git checkout -b L06_6.x origin/L06_6.x

安装扩展
composer install
npm install
npm run dev
修改配置文件
由于容器在同一网络中，这里的host直接填容器名称就可以连接。
...
DB_HOST=mysql-master
...
ES_HOSTS=elasticsearch

php artisan migrate
php artisan es:migrate

es:migrate会报错!!!
project/app/Console/Commands/Elasticsearch/Indices/ProjectIndex.php有如下定义:

...
'synonyms_path' => 'analysis/synonyms.txt',
...

在php和elasticsearch两个容器中都没有这个文件，电商项目源码中也没有，对elasticsearch还不会使用，暂时这样处理:
'filter'   => [
                    'synonym_filter' => [
                        'type'          => 'synonym',
                        // 'synonyms_path' => 'analysis/synonyms.txt',
                        "synonyms" => ["universe, cosmos"]
                    ],
                ],
再次执行php artisan es:migrate

至此，电商项目环境部署完毕。





