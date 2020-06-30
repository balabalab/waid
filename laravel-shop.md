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
