# 容器基本操作 (以Docker为例)

## 修改entrypoint 并进入交互模式运行完后删除

```bash
docker run -it --rm --entrypoint /bin/bash container-app:v0.x
```

## 运行时导入环境变量

```bash
docker run -it -e MYSQL_HOST='localhost' -e MYSQL_PORT container-app:v0.x
```

## 容器daemon 模式运行导入端口

```bash
docker run -d -p 8081:8080 container-app:v0.1
```

## 清除所有不再运行的镜像及退出的容器

```bash
docker system prune -a
```