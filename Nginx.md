# Nginx

> 下载：Docker pull Nginx；或https://nginx.org/download/nginx-1.26.2.zip

* **conf**：服务配置文件
* html：存放前端静态文件
* logs：存放日志文件

**Docker从安装Nginx到部署静态文件过程**

> /root/nginx/html和/root/nginx/nginx.conf必须存在，并且.conf文件中必须符合Nginx启动要求

```shell
docker run -d \
  --name nginx \
  -p 18080:18080 \
  -p 18081:18081 \
  -v /root/nginx/html:/usr/share/nginx/html \
  -v /root/nginx/nginx.conf:/etc/nginx/nginx.conf \
  --network hm-net \
  nginx
```

