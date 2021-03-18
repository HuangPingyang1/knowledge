##### docker是一个开源的应用容器引擎，开发者可以打包自己的应用到容器里面，然后迁移到其他机器的docker应用中，可以实现快速部署。如果出现的故障，可以通过镜像，快速恢复服务。
# 安装docker
##      1.删除旧版本：
    sudo yum remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-engine
##     2.安装依赖：

```

sudo yum install -y yum-utils \
  device-mapper-persistent-data \
  lvm2
```
## 添加软件源：
```
#官方源
    sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
使用阿里云的源会更快
    sudo yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
    rpm --import http://mirrors.aliyun.com/docker-ce/linux/centos/gpg
    yum makecache fast
```


##     3.安装docker-ce
    sudo yum install docker-ce 
    #默认安装最新版本的docker
    yum list docker-ce --showduplicates | sort -r
    #列出可用的版本，可选定版本安装：yum install docker-ce-20.10.0
##     4.启动docker
    systemctl start docker
##     5.配置文件(镜像加速)
    cat > /etc/docker/daemon.json << EOF
    {"registry-mirrors": [
    "https://dockerhub.azk8s.cn",
    "https://reg-mirror.qiniu.com"
     ]}
    EOF
    systemctl daemon-reload
    systemctl restart docker
    
##     6.容器使用
    从仓库拉取镜像
    docker pull centos
    运行容器
    docker run -i -t centos
     #-i:保持标准输入保持打开状态(即交互式操作)
     #-t:分配一个伪tty（即新建一个终端）
    后台运行
    docker run -itd --name=centos_test -p 8001:80 -p 8002:3306 --restart=always 镜像ID
     #8002:宿主机端口。 
     #3306:容器内部使用端口。
     
    启动已停止的容器
    docker ps -a 
    docker start containerid
    停止容器
    docker stop containerid
    交互式命令进入容器
    docker exec -it containerid command
    #docker exec -it container /bin/bash
    
    删除容器
    docker rm 容器ID
    批量删除未使用的容器
    docker rm $(docker ps -qa)
    删除镜像
    docker rmi imageid
    
    查看镜像仓库
    docker search nginx
## 7.Docker数据持久化
    1.volume：
    docker volume create my-vol //创建数据卷
    docker volume ls    //查看所有容器卷
    docker volume inspect edc-nginx-vol // 查看指定容器卷详情信息
    
    docker run -d --name=shared -v my-vol:/container/dir image-id
    
    2.Bind Mounts:
    //直接挂载主机目录到容器，若主机和容器的目录不存在会自动创建
    docker run -d --name=shared -v /www/wwwroot:/container/dir image-id
    //-v:宿主机路径：容器内路径
    volumes（创建挂载点，由挂载点到容器目录）：创建数据卷，便于查看和维护（常用）
    bind mounts（直接挂载主机目录到容器）：直接挂载宿主机目录易用性比较差，存储不了挂载点的信息（比较常用）
    
    查看镜像、容器、数据卷所占用的空间。
    docker system df
#### 8.docker commit定制镜像
    将容器修改后保存为新镜像
    docker commit --author "yuepu" -m="has update" 容器ID 镜像名：v1
        #--author指定修改的作者     -m(--message)记录本次修改的内容
    
    查看镜像修改历史
    docker history nginx：v2    
    
Jumpserver Docker-Compose

```
$ git clone https://github.com/jumpserver/Dockerfile.git
$ cd Dockerfile
$ cat .env
$ docker-compose up

build
$ cd docker-compose
$ cat .env
$ docker-compose -f docker-compose-build.yml up
#.env 的变量 用在 docker-compose 里面, 可以自己看下 可能还有一些未能检测到的问题, 尽量自己调试一遍后再使用
如果自己编译, 可以在 docker-compose 的 environment: 处加入 Version: $Version , 取代 Dockerfile 的 ARG
```
