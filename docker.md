2020/6/29
docker从不会到入门
拉取镜像
docker pull nginx:stable

nginx
docker run  --name nginx-master -p 8100:80 -v ~/docker/nginx/default.conf:/etc/nginx/conf.d/default.conf -v ~/docker/shop:/var/www/shop -d nginx:stable
--name 指定名称
nginx-master 容器名称
-p端口映射
8100本地端口
80容器端口
-v 挂载
/docker/nginx/default.conf  nginx配置文件
/etc/nginx/conf.d/default.conf 映射文件
/docker/shop 本地项目
/var/www/shop 映射到容器的项目地址   在容器中修改会影响到本地代码
-d 后台运行
nginx:stable 镜像



mysql
docker run --name mysql-master -p 8105:3306 -e MYSQL_ROOT_PASSWORD=root -d mysql:latest

php-fpm
docker run --name php-fpm  --link mysql-master -p 8103:9000 -v ~/docker/shop:/var/www/shop -d php:7.3-fpm

nginx
docker run  --name nginx-master --link php-fpm -p 8100:80 -v ~/docker/nginx/default.conf:/etc/nginx/conf.d/default.conf -v ~/docker/shop:/var/www/shop -d nginx:stable

--link   容器之间的连接

1.创建mysql-master容器
2.创建php-fpm容器，并连接mysql容器
php-fpm的项目中连接数据库时host可直接写为mysql-master
3.创建nginx容器，并连接php-fpm容器
location ~ \.php$ {
  ...
  fastcgi_pass   php-fpm:9000;
  ...
}
php-fpm为容器名称，直接转发给php容器进行处理。

docker images 查看镜像
docker ps -a 查看容器
docker exec -i -t 容器名/容器id  bash 交互式进入容器 
docker start/stop/restart 容器名/容器id 管理容器
docker rm 容器名/容器id 删除容器
docker rmi 镜像名/镜像id 删除镜像
docker rm $(docker ps -a -q)  删除停止容器



部署完基本环境后安装php扩展，进入容器后:
(https://my.oschina.net/antsky/blog/1591418)
类似这样快捷安装
docker-php-ext-install mysqli pdo pdo_mysql
或者pecl安装
apt-get update
apt-get install -y libmemcached-dev zlib1g-dev
pecl install swoole && docker-php-ext-enable swoole

Dockerfile
https://www.cnblogs.com/edisonchou/p/dockerfile_inside_introduction.html
以上均为初始化操作，不方便管理，因此可以将初始化方式做成配置文件的形势（Dockerfile）

FROM nginx:stable
LABEL maintainer="doijanky"
EXPOSE 80 443
WORKDIR /

docker build -f dockerfile -t name:tag .

-f dockerfile 指定dockerfile文件
-t 生成镜像名和标签名

compose
dockerfile依然麻烦 使用docker compose进行管理
时间有限 不再尝试。
















