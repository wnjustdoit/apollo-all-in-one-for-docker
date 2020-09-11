## apollo-all-in-one-for-docker
docker 一键部署 Apollo 分布式配置中心（简化部署流程和运维复杂性：直接使用官方开源镜像，在 docker-compose 的环境下运行 ./startup_{profile}.sh 即可）   
注：以下演示以 docker-compose 版本 1.25.0 为准

#### 使用 Apollo 至少要开三个服务，在容器化部署的过程中也踩了一些坑，于是自己动手写了个自动化部署运维脚本，解决了一些问题：
1. 一键部署（提前准备好各个环境的配置，如线下线上）；
2. 使用 docker-compose + wait_for_it.sh 实现根据服务依赖关系顺序启动；
3. 在容器默认 bridge 网络模式下，解决 eureka 注册中心的 ip 问题

#### 使用说明（以 V1.7.1 版本为例）
###### 第一步：初始化数据库
* https://github.com/ctripcorp/apollo/blob/v1.7.1/scripts/sql/apolloconfigdb.sql
* https://github.com/ctripcorp/apollo/blob/v1.7.1/scripts/sql/apolloportaldb.sql
* 更新注册中心地址：
```
update ApolloConfigDB.serverconfig set `value` = 'http://172.16.141.109:8761/eureka/,http://172.16.141.110:8761/eureka/,http://172.16.141.111:8761/eureka/' where `key` = 'eureka.service.url';
```

###### 第二步：更改各环境的配置 .env.{profile}

###### 第三步：对应于各环境的启动脚本 startup_{profile}.sh

#### 关于注册中心
###### 默认值（数据库表：ApolloConfigDB.serverconfig）
```http://localhost:8080/eureka/```

###### 使用内置的注册中心
* 在 .env.{profile} 设置环境变量 APOLLO_EUREKA_SERVER_ENABLED=true
* 更改数据库配置的注册中心地址，如：http://{宿主机 ip}:{configserver 端口}/eureka/

###### 使用外部的注册中心（默认支持）
* 直接更改数据库配置为外部的注册中心地址即可（当前服务所在的宿主机必须能够访问到的 ip 和端口）
