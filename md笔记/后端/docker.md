

[docker电子书](https://yeasy.gitbook.io/docker_practice/)

## docker和虚拟机的区别

![image-20220318190815814](https://notes-imgs.oss-cn-shanghai.aliyuncs.com/note-imgs/image-20220318190815814.png)

Host OS(主人操作系统）就是安装在你硬件设备上的系统，而Guest OS(客人操作系统）则是安装在虚拟机（VM）上面的系统。**docker的优势为启动速度快，占用体积小**



## Docker组件

### docker的C/S架构

docker客户端向docker服务器或者Docker守护进程发出请求，完成后给出响应。

docker同时提供了命令行工具和RESTful API

作为C/S架构，客户端和服务器是可以分开在不同机器上的。

![image-20220318194450980](https://notes-imgs.oss-cn-shanghai.aliyuncs.com/note-imgs/image-20220318194450980.png)





### docker 镜像与容器

用户基于镜像来运行自己的容器。容器中可以存放任意东西（web服务或者数据库都可）

### registry注册中心

docker用registry来保存用户构建的镜像，registry分为共有（docker hub）和私有





## docker 安装

### mac

```shell
brew install --cask docker
```





## docker命令

镜像相关：

* 查看镜像： `docker images`
* 在仓库里搜索镜像：`docker search xxx`
* 拉取镜像：`docker pull xxx`
* 删除镜像：`docker rmi 镜像id`



容器相关：

* 查看容器：`docker ps`

* 创建容器

  * 交互式创建容器：`docker run -it --name=容器名 镜像名：标签 /bin/bash`。-i表示运行容器， -t容器启动后进入命令行
    * 退出容器：`exit` 退出后容器就关闭状态了。

  ![image-20220320081737389](https://notes-imgs.oss-cn-shanghai.aliyuncs.com/note-imgs/image-20220320081737389.png)

  * 守护式方式创建容器：`docker run -di --name=容器名 镜像名 `
    * 登录守护容器：`docker exec -it 容器名 /bin/bash`

  

* 停止容器：`docker stop  容器名/容器id`

* 启动容器：`docker start 容器名/容器id `

* 删除容器：`docker rm  容器名/容器id`

  

  

* 文件拷贝：`docker cp 文件名 目标容器名:路径`，如果从容器中拷贝到Host OS 则把下面命令颠倒即可。

  ![image-20220320082445945](https://notes-imgs.oss-cn-shanghai.aliyuncs.com/note-imgs/image-20220320082445945.png)



* 目录挂载：*-v*    将**Host OS的目录和容器目录进行映射。这样对于Host OS的文件修改就直接影响容器。如果是多级目录可能出现权限不足的提示，是因为Centos安全模块把权限禁止了，需要添加参数`--privileged=true`来解决。

![image-20220320083218968](https://notes-imgs.oss-cn-shanghai.aliyuncs.com/note-imgs/image-20220320083218968.png)





* 查看容器信息（如IP地址）：`docker inspect 容器名`





## 常用应用部署

### mysql

* 查找合适的mysql镜像：`docker search mysql`

* 拉取：`docker pull 镜像名`

* 创建容器：`docker run -di --name=CentosMysql -p 33306:3306 -e MYSQL_ROOT_PASSWORD=123456 centos/mysql-57-centos7`

  ![image-20220320094101990](https://notes-imgs.oss-cn-shanghai.aliyuncs.com/note-imgs/image-20220320094101990.png)

  * `-p` 端口映射
  * `-e` 设置mysql密码

* 进入mysql容器：

* 登录mysql

* 远程登录mysql，比如通过navicate premium 登录，要把3306端口改为映射后的端口。



###  nginx

```sh
docker run -di --name=myNginx -p 80:80 nginx
```

nginx在etc目录下：

![image-20220320113507754](https://notes-imgs.oss-cn-shanghai.aliyuncs.com/note-imgs/image-20220320113507754.png)

从nginx.conf文件可以看到nginx的config的文件其实在： include /etc/nginx/conf.d/*.conf;

通过查看default.conf可以知道，nginx的默认html路径在：

![image-20220320114659694](https://notes-imgs.oss-cn-shanghai.aliyuncs.com/note-imgs/image-20220320114659694.png)

## 迁移与备份

#### 将容器保存为镜像

```shell
docker commit mynginx mynginx_image
```

#### 镜像保存为tar文件

```shell
docker save -o mynginx.tar mynginx_image
```

#### tar恢复为镜像

```shell
docker load -i mynginx.tar
```

















### dockerfile

通过一系列命令和参数，由基础镜像来构建新的镜像。

#### 常用命令

![image-20220320190204699](https://notes-imgs.oss-cn-shanghai.aliyuncs.com/note-imgs/image-20220320190204699.png)



#### dockerfile构建jdk镜像

![image-20220320191543939](https://notes-imgs.oss-cn-shanghai.aliyuncs.com/note-imgs/image-20220320191543939.png)

![image-20220320191602689](https://notes-imgs.oss-cn-shanghai.aliyuncs.com/note-imgs/image-20220320191602689.png)





## docker私有仓库



### 私有仓库的搭建

![image-20220320192027204](https://notes-imgs.oss-cn-shanghai.aliyuncs.com/note-imgs/image-20220320192027204.png)



### 将镜像上传到私有仓库

![image-20220320192137971](https://notes-imgs.oss-cn-shanghai.aliyuncs.com/note-imgs/image-20220320192137971.png)







## docker compose









