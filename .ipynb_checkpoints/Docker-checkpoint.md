# Docker

### 容器管理相关命令

1. `docker run [options] IMAGE`：从镜像启动一个新容器。
   - 常用选项：
     - `-d`：后台运行容器（即守护模式）。
     - `-p`：端口映射，例如 `-p 8080:80`。
     - `-v`：挂载卷（目录），例如 `-v /host/path:/container/path`；docker默认数据卷在`/var/lib/docker/volumes`；
       - 例`docker run -d --name nginx -p 80:80 -v html:/usr/share/nginx/html nginx`：以html:开头默认挂载到docker的volume卷目录下；以./或/开头则挂载到本机的任意自定义目录下
     - `--name`：为容器命名。
     - `-e key = value`：设置环境变量
     - `--network [网络名]`
2. `docker ps`：列出正在运行的容器。
   - `-a`：列出所有容器（包括停止的容器）。
3. `docker stop CONTAINER`：停止一个正在运行的容器。
4. `docker start CONTAINER`：启动一个已停止的容器。
5. `docker restart CONTAINER`：重启一个容器。
6. `docker rm CONTAINER`：删除一个停止的容器。
   - `-f`：强制删除一个正在运行的容器。`docker rm nginx -f`
7. `docker exec [options] CONTAINER COMMAND`：在运行的容器中执行命令。exit退出容器
   - `-it`：以交互模式运行，例如进入容器的 shell：`docker exec -it Nginx bash`
8. `docker logs [OPTIONS] CONTAINER`：查看容器的日志输出。
   * `-f`：持续输出日志
9. `docker inspect CONTAINER`：查看容器或镜像的详细信息。能看容器网关和IP地址
10. `docker top CONTAINER`：查看容器内运行的进程。
11. `docker save [OPTIONS] IMAGE [IMAGE...]`：例docker save -o nginx.tar nginx:latest
12. `docker load [OPTIONS]`：
    * `-i`：读取文件
    * `-q`：读取时不要输出任何内容

### 镜像管理相关命令

11. `docker images`：列出本地的所有镜像。

12. `docker pull IMAGE`：从 Docker 仓库中拉取镜像。

13. `docker rmi IMAGE`：删除一个镜像。
   - `-f`：强制删除一个镜像。

14. **`docker build [options] PATH`**：根据 Dockerfile 构建镜像。
   - `-t`：为构建的镜像命名和打标签。

15. **`docker commit CONTAINER IMAGE`**：将容器的更改提交为新镜像。

16. **`docker tag SOURCE_IMAGE[:TAG] TARGET_IMAGE[:TAG]`**：为镜像打标签。

### 网络与卷相关命令

17. `docker network ls`：列出所有 Docker 网络

18. `docker network connect [网络名] [容器名]`：使指定容器连接加入某网络

19. `docker network disconnect`：使指定容器连接离开某网络

20. `docker network prune`：清除所有没有用到的网络

21. **`docker network create NETWORK`**：创建自定义 Docker 网络

22. **`docker network inspect NETWORK`**：查看网络详细信息

23. **`docker volume ls`**：列出所有 Docker 卷

24. `docker volume inspect [挂载名称]`：查看挂载信息；

25. **`docker volume create VOLUME`**：创建 Docker 卷

26. **`docker volume rm VOLUME`**：删除 Docker 卷

27. `docker volume prune`：删除未使用的数据卷

### 系统管理相关命令

23. **`docker system df`**：查看 Docker 使用的磁盘空间
24. **`docker system prune`**：清理未使用的容器、镜像、卷和网络
25. **`docker stats`**：实时查看容器的资源使用情况

## 基础知识

> 安装docker：
>
> 1. 卸载旧版本docker
>
>    ```shell
>    $  sudo yum remove docker \
>                      docker-client \
>                      docker-client-latest \
>                      docker-common \
>                      docker-latest \
>                      docker-latest-logrotate \
>                      docker-logrotate \
>                      docker-engine
>    ```
>
> 2. 安装docker
>
>    ```shell
>    curl -fsSL https://get.docker.com | bash -s docker --mirror Aliyun
>    # 安装完毕后记得启动服务
>    service docker start
>    ```
>
> 3. 配置镜像加速
>    1. 打开vim /etc/docker/daemon.json配置文件
>
>    2. 添加
>
>       ```shell
>       # 腾讯云镜像\阿里云镜像
>       {
>          "registry-mirrors": [
>          		"https://mirror.ccs.tencentyun.com",
>          		"https://r3hv0czs.mirror.aliyuncs.com",
>          		"https://docker.zhai.cm"
>          ]
>       }
>       ```
>    
>    3. 重启docker
>
>       ```shell
>       systemctl restart docker
>       ```
>

### 常用指令

```shell
sudo usermod -aG docker $USER
```

> 执行完此操作后，需要退出服务器，再重新登录回来，可以省去sudo权限。

---

配置MySQL8.0.26

```shell
# 自定义挂载目录
docker run \
-p 3306:3306 \
--name mysql8 \
--privileged=true \
-v /home/mysql/log:/var/log/mysql \
-v /home/mysql/data:/var/lib/mysql \
-v /home/mysql/conf:/etc/mysql/conf.d \
-v /homt/mysql/init:/docker-entrypoint-initdb.d \
-e MYSQL_ROOT_PASSWORD=123456 \
-d mysql:8.0.26
# 做docker默认挂载目录
docker run \
--privileged=true \
-p 3306:3306 --name mysql8 \
-e MYSQL_ROOT_PASSWORD=123456 \
-d mysql:8.0.26

docker run -d --name nginx -p 80:80 -v html:/usr/share/nginx/html -v conf:/etc/nginx/nginx.conf nginx
```

**操作镜像文件**

1. **拉取**镜像

   ```shell
   docker pull centos:7.6.1810
   ```

2. **查看**本地所有镜像

   ```shell
   docker images
   ```

3. **删除**镜像

   ```shell
   # 删除容器 docker rm 容器name
   docker rmi centos:7.6.1810
   ```

4. 生成某个container的镜像,会**生成一个container.tar**的文件

   ```shell
   # [container]表示指令可写也可不写
   docker [container] commit CONTAINER IMAGE_NAME:TAG
   ```

5. 将镜像centos:7.6.1810**导出**到本地文件**centos:7.6.1810.tar**中

   ```shell
   # 迁移镜像操作 生成后该文件只有自己可读可写
   docker save -o centos:7.6.1810.tar centos:7.6.1810
   ```

6. 将镜像centos:7.6.1810从本地文件**centos:7.6.1810.tar**中**加载**出来

   ```shell
   # 将其他刻好的镜像加载到当前宿主机docker镜像文件中
   docker load -i centos:7.6.1810.tar
   ```

**容器操作详解**

1. 查看正在运行的所有容器

   ```shell
   # docker ps -a ：查看所有容器
   docker ps
   ```

2. 创建并启动一个容器

   ```shell
   # -d：后台运行容器
   docker run -itd --name mydocker -p 8081:8080 centos /bin/bash
   # [ -p ]: 配置端口映射（容器的8080映射到Linux主机的8081端口）
   ```

3. 启动所有容器命令

   ```shell
   docker start $(docker ps -a | awk '{ print $1}' | tail -n +2)
   ```

4. 关闭所有容器命令

   ```shell
   docker stop $(docker ps -a | awk '{ print $1}' | tail -n +2)
   ```

5. 删除所有容器命令

   ```shell
   docker rm $(docker ps -a | awk '{ print $1}' | tail -n +2)
   ```

6. 删除所有镜像命令

   ```shell
   docker rmi $(docker images | awk '{print $3}' |tail -n +2)
   ```

### 常见错误

* 发布容器后报错：WARNING: IPv4 forwarding is disabled. Networking will not work.

  ```shell
  vim /etc/sysctl.conf
  net.ipv4.ip.ip_forward=1  #添加此配置
  
  # 重启docker和network服务
  systemctl restart docker && systemctl restart network
  
  # 查看是否修改成功
  systemctl net.ipv4.ip.ip_forward
  ```
  
* docker中yum下载包时报错Error: Failed to download metadata for repo ‘appstream‘: Cannot prepare internal mirrorlist

  ```shell
  # 检查网络是否正常
  ping www.baidu.com
  # 网络正常但是centOS库中停止维护问题看如下解决办法
  # 1.进入yum 的 repos 目录
  cd /etc/yum.repos.d/
  # 2.修改 centos 文件内容
  sed -i 's/mirrorlist/#mirrorlist/g' /etc/yum.repos.d/CentOS-*
  sed -i 's|#baseurl=http://mirror.centos.org|baseurl=http://vault.centos.org|g' /etc/yum.repos.d/CentOS-*
  # 3.生成缓存更新
  yum makecache
  # 4.运行 yum update 并重新安装 vim
  yum update -y
  yum -y install vim
  ```

### DockerFile

> 类似exe文件执行文本文件中的所有指令

* FROM
* ENV
* COPY
* RUN
* EXPOSE
* ENTRYPOINT

例：

```shell
# this docker file
# VERSION 1.0
# email:basecloudonly@163.com
# 基于cntos7，如果没有这个镜像那么它会下载这个镜像
FROM centos:7
# 创建者
MAINTAINER SansSouciHuangmin@163.com
# 为Dockerfile中所有RUN、CMD、ENTRYPOINT、COPY和ADD指令设定工作目录
WORKDIR /usr
# 执行命令(这里在容器内部创建了一个目录)
RUN mkdir /usr/local/java
# 和copy一样，复制文件到指定（当前）目录，但是copy不能解压，add自动解压
ADD xxxxxxxxxxxxxxxxx.jar .
# java命令不生效则重新下载tar.gz文件
ADD jdk-8u361-linux-x64.tar.gz /usr/local/java
# 重命名(不知道文件名可以现在宿主机解压后看一下)（可以不设置）
RUN ln -s /usr/local/java/jdk1.8.0_361 /usr/local/java/jdk

# 设置环境变量
ENV JAVA_HOME /usr/local/java/jdk1.8.0_361
ENV JRE_HOME ${JAVA_HOME}/jre
ENV CLASSPATH .:${JAVA_HOME}/lib/dt.jar:${JAVA_HOME}/lib/tools.jar:${JRE_HOME}/lib 
ENV PATH ${PATH}:${JAVA_HOME}/bin:${JRE_HOME}/bin

# 运行jar文件，并且在后台持续运行
CMD ["nohup" ,"java", "-jar", "xxxxxxxxxxxxxxx.jar &"]
```

### DockerCompose

> 做多容器操作，通过docker-compose.yml定义一组关联的应用容器，帮助实现多个相互关联的Docker容器的快速部署

**常用命令**格式：`docker compose [OPTIONS] [COMMAND]`

* `OPTIONS`
  * -f：指定compose文件路径和名称
  * -p：指定project名称
  * -d：后台运行命令
* `COMMAND`
  * up：创建并启动所有service容器
  * down：停止并移除所有容器、网络
  * ps：列出所有启动的容器
  * logs：查看指定容器的日志
  * stop：停止容器
  * start：启动容器
  * restart：重启容器
  * top：查看运行的进程
  * exec：在指定的运行中的容器中执行命令

实例：

docker-compose.yml文件内容

```yaml
version: "3.8"

services:
	mysql:
		image: mysql
		container_name: mysql
		ports:
			- "3306:3306"
		environment:
			TZ: Asiz/Shanghai
			MYSQL_ROOT_PASSWORD: 123456
		volumes:
			- "./mysql/conf:/etc/mysql/conf.d"
			- "./mysql/data:/var/lib/mysql"
			- "./mysql/init:/docker-entrypoint-initdb.d/"
		networks:
			- hm-net
	hmProject:
	# 通过Dockerfile构建项目，项目路径在当前yml文件路径下.
		build:
			context: .
			dockerfile: Dockerfile
		container_name: hmProject
		ports:
			- "8080:8080"
		networks:
			-hm-net
		depends_on:
			-mysql
	nginx:
		image: nginx
		container_name: nginx
		ports:
			- "80:80"
		volumes:
			- "./nginx/html:/usr/share/nginx/html"
			- "./nginx/nginx.conf:/etc/nginx/nginx.conf"
		depends_on:
			- hmProject
		networks:
			- hm-net
networks:
	hm-net:
		name: hmProject
```

