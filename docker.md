#### 2020/6/29
#### docker从不会到入门
    docker常用命令
        docker pull 拉取镜像
        docker run  运行容器
        docker images 查看镜像
        docker ps -a 查看容器
        docker exec -i -t 容器名/容器id  bash 交互式进入容器 
        docker start/stop/restart 容器名/容器id 管理容器
        docker rm 容器名/容器id 删除容器
        docker rmi 镜像名/镜像id 删除镜像
        docker rm $(docker ps -a -q)  删除已停止的容器
        docker network create  创建网络 
        docker network ls 查看网络
        docker inspect  网络详情


#### nginx容器构建
    docker pull nginx:stable
    docker run  --name nginx-master -p 8100:80 -v ~/docker/nginx/default.conf:/etc/nginx/conf.d/default.conf -v ~/docker/shop:/var/www/shop -d nginx:stable
        --name 指定名称  nginx-master 容器名称
        -p     端口映射  8100本地端口  80容器端口
        -v 挂载
            /docker/nginx/default.conf     nginx配置文件
            /etc/nginx/conf.d/default.conf 映射文件
            /docker/shop  本地项目
            /var/www/shop 映射到容器的项目地址   在容器中修改会影响到本地代码
        -d 后台运行  
        nginx:stable 镜像


#### mysql容器
    docker run --name mysql-master -p 8105:3306 -e MYSQL_ROOT_PASSWORD=root -d mysql:latest

#### php-fpm容器
    docker run --name php-fpm  -p 8103:9000 -v ~/docker/shop:/var/www/shop -d php:7.3-fpm

#### 容器互联
    --link 参数将要废弃不再使用。因此使用自定义网络的形式，具体教程可参考：
    https://blog.csdn.net/qingyafan/article/details/90680834
#### php运行环境
    1.创建mysql-master、php-fpm、nginx-master
    2.创建自定义网络
        docker network create somenetwork
    3.将各容器加入同一网络
        docker network connect somenetwork mysql-master
        docker network connect somenetwork php-fpm
        docker network connect somenetwork nginx-master
    3.在容器中请求其他容器
        nginx的配置文件php文件请求配置可写为容器名。
        location ~ \.php$ {
          ...
          fastcgi_pass   php-fpm:9000;
          ...
        }
        同理php容器中mysql操作的host可写为mysql-master(容器名)
    4.安装扩展
        首先进入容器:docker exec -i -t php-fpm bash
        安装方法：
            1）docker-php-ext-install快捷安装
                docker-php-ext-install mysqli pdo pdo_mysql
            2) pecl安装
                apt-get update
                apt-get install -y libmemcached-dev zlib1g-dev
                pecl install swoole && docker-php-ext-enable swoole
            3）源码安装和apt-get安装不再复述。
#### Dockerfile
    将容器初始化操作写成一个配置文件，使用配置文件构建容器。不是很方便，后面有专门的管理应用compose，这里直接跳过。














