# Kong 在线实验环境

## 软件简介

Kong旨在确保，管理和扩展Microservices和API。如果您正在构建Web，移动或物联网（物联网），则可能需要在实际软件之上实现常见功能。通过充当任何HTTP资源的网关，通过插件提供日志记录，身份验证和其他功能，可以帮助您。

由NGINX和Cassandra提供的重点是高性能和可靠性，Kong在Mashape生产，在这里处理了超过一千个API的数十亿个API请求。

所属类别是服务器软件

## 软件官网

https://getkong.org/

## Dockerfile 使用方法

### 如何使用
首先，在启动之前，Kong需要运行Cassandra 2.2.x或PostgreSQL 9.4 / 9.5集群。您可以使用官方Cassandra / PostgreSQL容器，或使用您自己的。

链接到Cassandra或PostgreSQL容器

由于Kong支持两者，因此您可以决定要使用的Cassandra或PostgreSQL之间的数据存储区。

Cassandra

通过执行以下命令启动Cassandra容器：
```
$ docker run -d --name kong-database \
                -p 9042:9042 \
                cassandra:2.2
```
Postgres
通过执行以下命令启动PostgreSQL容器：
```
docker run -d --name kong-database \
                -p 5432:5432 \
                -e "POSTGRES_USER=kong" \
                -e "POSTGRES_DB=kong" \
                postgres:9.4
```
### 开始
一旦数据库运行，我们可以启动一个Kong容器并将其链接到数据库容器，并KONG_DATABASE使用cassandra或postgres根据您决定使用的数据库配置环境变量：
```
$ docker run -d --name kong \
    --link kong-database:kong-database \
    -e "KONG_DATABASE=cassandra" \
    -e "KONG_CASSANDRA_CONTACT_POINTS=kong-database" \
    -e "KONG_PG_HOST=kong-database" \
    -p 8000:8000 \
    -p 8443:8443 \
    -p 8001:8001 \
    -p 7946:7946 \
    -p 7946:7946/udp \
    kong
```
如果一切顺利，如果您使用默认端口创建容器，则应该监听您的主机8000（[proxy] [ http://getkong.org/docs/latest/configuration/#proxy_port ]），8443（代理SSL）和8001（admin api）端口。端口7946（集群）仅由其他Kong节点使用。

### 使用自定义配置（和自定义Cassandra / PostgreSQL群集）
您可以使用环境变量覆盖Kong配置文件的任何属性。只需使用KONG_前缀添加任何Kong配置属性，例如：
```
$ docker run -d --name kong \
    -e "KONG_LOG_LEVEL=info" \
    -e "KONG_CUSTOM_PLUGINS=helloworld" \
    -e "KONG_PG_HOST=1.1.1.1" \
    -p 8000:8000 \
    -p 8443:8443 \
    -p 8001:8001 \
    -p 7946:7946 \
    -p 7946:7946/udp \
    kong
```
在运行容器中重新加载

如果您更改自定义配置，则可以通过以下方式重新加载Kong（无停机）：
```
$ docker exec -it kong kong reload
```
这将kong reload在容器中运行命令。

## 资源链接

- https://getkong.org/docs/
