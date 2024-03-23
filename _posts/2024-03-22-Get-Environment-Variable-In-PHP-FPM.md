# Get Environment Variable In PHP-FPM

在构建 Docker 镜像的时候发现声明了 **ENV** ，但PHP程序中无法获取

Dockerfile 内容如下

```Dockerfile
FROM xxx.xxx.com/ckafka/php-fpm81:v1.0-stable

WORKDIR /app

ENV ADMIN_LOG_HOME=/data/log/kafka-admin
ENV ADMIN_OP_LOG_HOME=/data/log/kafka-admin-op

RUN mkdir -p /app/kafka-admin /data/log/kafka-admin /data/log/kafka-admin-op
COPY . /app/kafka-admin/
COPY conf/server.conf /etc/nginx/conf.d/admin.conf

COPY entrypoint.sh /app/entrypoint.sh
RUN chmod +x  /app/entrypoint.sh

ENTRYPOINT ["/app/entrypoint.sh"]
```

实际运行代码

```php
$dir = getenv("ADMIN_LOG_HOME") === false ? SITE_LOG_DIR : getenv("ADMIN_LOG_HOME");

if (!file_exists($dir)) {
    if (!mkdir($dir, 0770)) {
        return;
    }
}
```

运行结果：

```bash
# getenv("ADMIN_LOG_HOME")
bool(false)
```

## 探究原因

Dockerfile 中声明的环境变量只针对 **php-cli** 环境生效， 对于 **php-fpm** 环境仍是不可见的

## 修正方法

再nginx 配置文件中加入 **``fastcgi_param``**

```
fastcgi_param ADMIN_LOG_HOME /data/log/kafka-admin/;
```

完整配置上下文如下：

```
server{
    server_name localhost.domain;
    listen 8088;
    access_log  /data/log/project/access_admin_project.log;
    error_log  /data/log/project/error_admin_project.log;

    location ^~ /static/ {
        root /app/project-admin/view/;
    }

    location / {
        root /app/project-admin/;
        index index.php;
        try_files $uri $uri/ /index.php?$query_string;
    }


    location ~ \.php$ {
        root    /app/kafka-admin/;
        # fastcgi_pass 127.0.0.1:9000;
        fastcgi_pass unix:/run/php/php8.1-fpm.sock;
        fastcgi_index index.php;
        fastcgi_param SCRIPT_FILENAME  $document_root$fastcgi_script_name;
        fastcgi_param ADMIN_LOG_HOME /data/log/kafka-admin/;
        keepalive_timeout 1800;
        fastcgi_connect_timeout 1800;
        fastcgi_send_timeout 1800;
        fastcgi_read_timeout 1800;
        include fastcgi_params;
    }
}
```

- 重启动nginx 即可
