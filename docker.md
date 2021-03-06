<!-- TOC START min:1 max:3 link:true asterisk:false update:true -->
- [1、镜像与容器基本操作](#1镜像与容器基本操作)
  - [1、镜像](#1镜像)
  - [2、容器](#2容器)
- [2、导入与导出镜像](#2导入与导出镜像)
  - [1、使用 save 保存镜像至文件](#1使用-save-保存镜像至文件)
  - [2、使用 load 导入镜像文件](#2使用-load-导入镜像文件)
  - [3、使用 export 保存镜像至文件](#3使用-export-保存镜像至文件)
  - [4、使用 import 导入镜像文件](#4使用-import-导入镜像文件)
  - [5、区别](#5区别)
  - [6、建议](#6建议)
- [3、从指定仓库拉取或推送镜像](#3从指定仓库拉取或推送镜像)
<!-- TOC END -->



# 1、镜像与容器基本操作
## 1、镜像

>删除镜像  
docker rmi allen_mysql:5.7

>查看所有镜像  
docker images

>搜索镜像  
docker search centos

>删除所有的镜像  
docker rmi $(docker images -q)

>镜像构建  
-t 表示新构建镜像的名称和TAG  
. 表示使用当前目录的Dockerfile构建镜像  
docker build -t ruolihello:v1.0.0 .


## 2、容器
>删除容器  
docker rm e1a4cb7b4fe3

>查看正在运行的容器  
docker ps

>查看所有容器  
docker ps -a

>停止所有的容器  
docker stop $(docker ps -aq)

>删除所有的容器  
docker rm $(docker ps -aq)

>运行容器  
-p 映射端口  
-d 后台运行，返回容器ID  
docker run -p 8080:8080 -d ruolihello:v1.0.0


现在的docker有了专门清理资源(container、image、网络)的命令。   
docker 1.13 中增加了docker system prune的命令，针对container、image可以使用docker container prune、docker image prune命令。

>删除所有不使用的镜像  
docker image prune --force --all  
或  
docker image prune -f -a

>删除所有停止的容器  
docker container prune


# 2、导入与导出镜像
涉及的命令有export、import、save、load
## 1、使用 save 保存镜像至文件

>docker save -o nginx.tar nginx:latest

其中-o和>表示输出到文件，nginx.tar为目标文件，nginx:latest是源镜像名（name:tag）  
## 2、使用 load 导入镜像文件
>docker load -i nginx.tar

其中-i表示从文件输入。会成功导入镜像及相关元数据，包括tag信息  
## 3、使用 export 保存镜像至文件
>docker export -o nginx-test.tar nginx-test

其中-o表示输出到文件，nginx-test.tar为目标文件，nginx-test是源容器名（name）
## 4、使用 import 导入镜像文件
>docker import nginx-test.tar nginx:imp

## 5、区别
1. export命令导出的tar文件略小于save命令导出的
2. export命令是从容器（container）中导出tar文件，而save命令则是从镜像（images）中导出
3. 基于第二点，export导出的文件再import回去时，无法保留镜像所有历史（即每一层layer信息，不熟悉的可以去看Dockerfile），不能进行回滚操作；而save是依据镜像来的，所以导入时可以完整保留下每一层layer信息。
## 6、建议
可以依据具体使用场景来选择命令
1. 若是只想备份images，使用save、load即可
2. 若是在启动容器后，容器内容有变化，需要备份，则使用export、import

# 3、从指定仓库拉取或推送镜像
1. 拉取镜像
>docker pull openjdk:8-jre-alpine registry.cn-shanghai.aliyuncs.com/ruoli-microservice/openjdk:8-jre-alpine

2. 标记镜像
>docker tag openjdk:8-jre-alpine registry.cn-shanghai.aliyuncs.com/ruoli-microservice/openjdk:8-jre-alpine

3. 推送镜像
>docker push openjdk:8-jre-alpine registry.cn-shanghai.aliyuncs.com/ruoli-microservice/openjdk:8-jre-alpine


# 4、Docker示例
```bash
FROM openjdk:8u265-jre
ARG workdir=/app
WORKDIR ${workdir}
ADD ruolihello.jar app.jar
EXPOSE 8080
ENTRYPOINT ["java","-jar","app.jar"]
```
