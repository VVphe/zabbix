# zabbix部署过程

## zabbix server部署
### 拉取镜像
- mysql镜像：`docker pull mysql:5.6`
- zabbix server镜像：`docker pull zabbix/zabbix-server-mysql`
- zabbix web接口镜像：`docker pull zabbix/zabbix-web-nginx-mysql`

### 运行容器
- 运行mysql server实例
```
    docker run --name mysql-server -t -e MYSQL_DATABASE="zabbix" -e MYSQL_USER="zabbix" -e MYSQL_PASSWORD="123456" -e MYSQL_ROOT_PASSWORD="123456" -p 3309:3306 -d mysql:5.6
```
- 运行zabbix server实例，并关联到已创建的mysql数据库
```
    docker run --name zabbix-server -t -e DB_SERVER_HOST="mysql-server" -e MYSQL_DATABASE="zabbix" -e MYSQL_USER="zabbix" -e MYSQL_PASSWORD="123456" -e MYSQL_ROOT_PASSWORD="123456" --link mysql-server:mysql -p 10051:10051 -d zabbix/zabbix-server-mysql
```
- 运行zabbix web接口实例，关联到zabbix server及mysql数据库
```
    docker run --name zabbix-web -t -e DB_SERVER_HOST="mysql-server" -e MYSQL_DATABASE="zabbix" -e MYSQL_USER="zabbix" -e MYSQL_PASSWORD="123456" -e MYSQL_ROOT_PASSWORD="123456" --link mysql-server:mysql --link zabbix-server:zabbix-server -p 8088:80 -d zabbix/zabbix-web-nginx-mysql
```
- 浏览器输入主机IP和相应端口，出现zabbix登录界面，默认用户名密码Admin/zabbix
    ![zabbix_login](https://raw.githubusercontent.com/VVphe/zabbix_learning/master/res/zabbix_login.png)

## zabbix agent部署
### 安装zabbix agent
    ubuntu系统下：apt-get install zabbix-agent

### 修改配置文件
    vim /etc/zabbix/zabbix_agentd.conf
    修改Server、ActiveServer为server端IP地址，Hostname为被监控主机名
    获得server端IP：docker inspect zabbix-server|grep IP

### 运行zabbix agent
    service zabbix-agent start

[***官方文档***](https://www.zabbix.com/documentation/)