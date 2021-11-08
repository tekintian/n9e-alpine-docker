# n9e 5.0 alpine

DIDI N9E 夜莺最新Alpine容器版
核心服务使用 alpine 最小化容器


## 数据库创建
数据库环境变量, 注意这里的N9E_DB_URL 对应的192.168.2.8只能使用IP地址,不能用域名或者主机名(这个应该是个BUG),因为所有的主机都会被解析到 127.0.0.1 导致数据库无法连接!

N9E_DB_URL="192.168.2.8:3306"
N9E_DB_NAME="n9e"
N9E_DB_USER="n9e"
N9E_DB_PWD="n9e999"

数据库脚本文件:
n9e-server/server/sql/n9e.sql

## Prometheus 容器创建
~~~sh
docker run --name prometheus -itd \
    -p 9090:9090 \
    -v /opt/prometheus/data:/opt/bitnami/prometheus/data \
    --restart unless-stopped \
    bitnami/prometheus:2.28.0
~~~

## n9e alpine
N9E alpine容器连接prometheus容器, 连接外部数据库, 需要先使用数据库脚本创建并配置好连接用户
注意替换相应的变量值为你自己的
~~~sh
docker run --name n9e -itd \
    -p 5002:8000 \
    -p 5092:9000 \
    -e N9E_DB_URL="192.168.2.8:3306" \
    -e N9E_DB_NAME="n9e" \
    -e N9E_DB_USER="n9e" \
    -e N9E_DB_PWD="n9e999" \
    -v /n9e/data:/data \
    --link prometheus \
    --restart unless-stopped \
    tekintian/n9e-alpine:5.0.0-rc7
~~~

## 使用
http://localhost:8000/

默认登录用户名: root  密码: root.2020

