# Docker

### 容器管理相关命令

1. **`docker run [options] IMAGE`**：从镜像启动一个新容器。
   - 常用选项：
     - `-d`：后台运行容器（即守护模式）。
     - `-p`：端口映射，例如 `-p 8080:80`。
     - `-v`：挂载卷（目录），例如 `-v /host/path:/container/path`。
     - `--name`：为容器命名。
     - `-e`：设置环境变量。

2. **`docker ps`**：列出正在运行的容器。
   - `-a`：列出所有容器（包括停止的容器）。

3. **`docker stop CONTAINER`**：停止一个正在运行的容器。

4. **`docker start CONTAINER`**：启动一个已停止的容器。

5. **`docker restart CONTAINER`**：重启一个容器。

6. **`docker rm CONTAINER`**：删除一个停止的容器。
   - `-f`：强制删除一个正在运行的容器。

7. **`docker exec [options] CONTAINER COMMAND`**：在运行的容器中执行命令。
   - `-it`：以交互模式运行，例如进入容器的 shell：`docker exec -it CONTAINER /bin/bash`。

8. **`docker logs CONTAINER`**：查看容器的日志输出。

9. **`docker inspect CONTAINER`**：查看容器或镜像的详细信息。

10. **`docker top CONTAINER`**：查看容器内运行的进程。

### 镜像管理相关命令

11. **`docker images`**：列出本地的所有镜像。

12. **`docker pull IMAGE`**：从 Docker 仓库中拉取镜像。

13. **`docker rmi IMAGE`**：删除一个镜像。
   - `-f`：强制删除一个镜像。

14. **`docker build [options] PATH`**：根据 Dockerfile 构建镜像。
   - `-t`：为构建的镜像命名和打标签。

15. **`docker commit CONTAINER IMAGE`**：将容器的更改提交为新镜像。

16. **`docker tag SOURCE_IMAGE[:TAG] TARGET_IMAGE[:TAG]`**：为镜像打标签。

### 网络与卷相关命令

17. **`docker network ls`**：列出所有 Docker 网络。

18. **`docker network create NETWORK`**：创建自定义 Docker 网络。

19. **`docker network inspect NETWORK`**：查看网络详细信息。

20. **`docker volume ls`**：列出所有 Docker 卷。

21. **`docker volume create VOLUME`**：创建 Docker 卷。

22. **`docker volume rm VOLUME`**：删除 Docker 卷。

### 系统管理相关命令

23. **`docker system df`**：查看 Docker 使用的磁盘空间。
24. **`docker system prune`**：清理未使用的容器、镜像、卷和网络。
25. **`docker stats`**：实时查看容器的资源使用情况。

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

**配置端口映射**

```shell
# 将主机的8081端口映射到容器的8080端口
# mydocker（容器名称）
# centos（镜像REPOSITORY） 
docker run -it -d --name mydocker -p 8081:8080 centos /bin/bash
```

---

配置MySQL8.0.26

```shell
# 带挂载目录
docker run \
-p 3306:3306 \
--name mysql8 \
--privileged=true \
-v /home/mysql/log:/var/log/mysql \
-v /home/mysql/data:/var/lib/mysql \
-v /home/mysql/conf/my.cnf:/etc/mysql/my.cnf \
-v /home/mysql/conf.d:/etc/mysql/conf.d \
-e MYSQL_ROOT_PASSWORD=123456 \
-d mysql:8.0.26
# 不做挂载目录
docker run \
--privileged=true \
-p 3306:3306 --name mysql8 \
-e MYSQL_ROOT_PASSWORD=123456 \
-d mysql:8.0.26
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