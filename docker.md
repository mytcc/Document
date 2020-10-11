

### 1、镜像与容器基本操作
#### 1、镜像
```Bash
--删除镜像
docker rmi allen_mysql:5.7
#查看所有镜像
docker images
```

#### 1、容器
```Bash
--删除容器
docker rm e1a4cb7b4fe3
--查看正在运行的容器
docker ps
--查看所有容器
docker ps -a
```
### 2、导入与导出镜像
涉及的命令有export、import、save、load
#### 1、使用save保存镜像至文件
```Bash
docker save -o nginx.tar nginx:latest
```
或者  
```Bash
docker save > nginx.tar nginx:latest  
```
其中-o和>表示输出到文件，nginx.tar为目标文件，nginx:latest是源镜像名（name:tag）  
#### 2、使用load导入镜像文件
```Bash
docker load -i nginx.tar
```
或
```Bash
docker load < nginx.tar
```
其中-i和<表示从文件输入。会成功导入镜像及相关元数据，包括tag信息  
#### 3、使用export保存镜像至文件
```Bash
docker export -o nginx-test.tar nginx-test
```
其中-o表示输出到文件，nginx-test.tar为目标文件，nginx-test是源容器名（name）
#### 4、使用import导入镜像文件
```
docker import nginx-test.tar nginx:imp
```
或
```
cat nginx-test.tar | docker import - nginx:imp
```
#### 5、区别
1. export命令导出的tar文件略小于save命令导出的
2. export命令是从容器（container）中导出tar文件，而save命令则是从镜像（images）中导出
3. 基于第二点，export导出的文件再import回去时，无法保留镜像所有历史（即每一层layer信息，不熟悉的可以去看Dockerfile），不能进行回滚操作；而save是依据镜像来的，所以导入时可以完整保留下每一层layer信息。
#### 6、建议
可以依据具体使用场景来选择命令
1. 若是只想备份images，使用save、load即可
2. 若是在启动容器后，容器内容有变化，需要备份，则使用export、import

### 3、从指定仓库拉取或推送镜像
1. 拉取镜像
```Bash
docker pull openjdk:8-jre-alpine registry.cn-shanghai.aliyuncs.com/ruoli-microservice/openjdk:8-jre-alpine
```
2. 标记镜像
```Bash
docker tag openjdk:8-jre-alpine registry.cn-shanghai.aliyuncs.com/ruoli-microservice/openjdk:8-jre-alpine
```
3. 推送镜像
```Bash
docker push openjdk:8-jre-alpine registry.cn-shanghai.aliyuncs.com/ruoli-microservice/openjdk:8-jre-alpine
```
