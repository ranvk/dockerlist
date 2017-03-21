# dockerlist
**收集docker列表**

git clone git@github.com:ranvk/dockerlist.git
cd dockerlist

**centos73_php71   php7.1运行环境**
cd centos73_php71
docker build -t="uker/yii2zabbix" .

**zabbix运行环境**
 1. Start empty MySQL server instance

# docker run --name mysql-server -t \
      -e MYSQL_DATABASE="zabbix" \
      -e MYSQL_USER="zabbix" \
      -e MYSQL_PASSWORD="zabbix_pwd" \
      -e MYSQL_ROOT_PASSWORD="root_pwd" \
      -d mysql:5.7

2. Start Zabbix Java gateway instance

# docker run --name zabbix-java-gateway -t \
      -d zabbix/zabbix-java-gateway:latest

3. Start Zabbix server instance and link the instance with created MySQL server instance

# docker run --name zabbix-server-mysql -t \
      -e DB_SERVER_HOST="mysql-server" \
      -e MYSQL_DATABASE="zabbix" \
      -e MYSQL_USER="zabbix" \
      -e MYSQL_PASSWORD="zabbix_pwd" \
      -e MYSQL_ROOT_PASSWORD="root_pwd" \
      -e ZBX_JAVAGATEWAY="zabbix-java-gateway" \
      --link mysql-server:mysql \
      --link zabbix-java-gateway:zabbix-java-gateway \
      -p 10051:10051 \
      -d zabbix/zabbix-server-mysql:latest

Zabbix server instance exposes 10051/TCP port (Zabbix trapper) to host machine.

4. Start Zabbix web interface and link the instance with created MySQL server and Zabbix server instances

# docker run --name zabbix-web-nginx-mysql -t \
      -e DB_SERVER_HOST="mysql-server" \
      -e MYSQL_DATABASE="zabbix" \
      -e MYSQL_USER="zabbix" \
      -e MYSQL_PASSWORD="zabbix_pwd" \
      -e MYSQL_ROOT_PASSWORD="root_pwd" \
      --link mysql-server:mysql \
      --link zabbix-server-mysql:zabbix-server \
      -p 80:80 \
      -d zabbix/zabbix-web-nginx-mysql:latest