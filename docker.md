### Docker

搜索镜像

```
docker search 镜像名
```

列出镜像

```
docker images
```

列出所有的容器 ID

```
docker ps -aq
```

停止所有的容器

```
docker stop $(docker ps -aq)
```

删除所有的容器

```
docker rm $(docker ps -aq)
```

删除所有的镜像

```
docker rmi $(docker images -q)
```

复制文件

```
docker cp mycontainer:/opt/file.txt /opt/local/
docker cp /opt/local/file.txt mycontainer:/opt/
```



**docker-compose**

> Compose 是 docker 提供的一个命令行工具，用来定义和运行由多个容器组成的应用。
> 使用 compose，我们可以通过 YAML 文件声明式的定义应用程序的各个服务，并由单个命令完成应用的创建和启动。

后台构建部署

```
docker-compose up -d
```

docker-compose开始

```

```

docker-compose停止

```
docker-compose down
```

删除正在运行的容器

```
docker-compose rm
```

显示所有容器

```
docker-compose ps
```

