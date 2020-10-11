### 1、导入与导出镜像
涉及的命令有export、import、save、load
#### 1、使用save保存镜像至文件
```Bash
docker save -o nginx.tar nginx:latest
```
或者  
docker save > nginx.tar nginx:latest  
其中-o和>表示输出到文件，nginx.tar为目标文件，nginx:latest是源镜像名（name:tag）  
#### 2、使用load导入镜像文件  
docker load -i nginx.tar  
或
docker load < nginx.tar  
其中-i和<表示从文件输入。会成功导入镜像及相关元数据，包括tag信息  
#### 3、使用export保存镜像至文件
docker export -o nginx-test.tar nginx-test  
其中-o表示输出到文件，nginx-test.tar为目标文件，nginx-test是源容器名（name）
#### 4、使用import导入镜像文件 
docker import nginx-test.tar nginx:imp
或
cat nginx-test.tar | docker import - nginx:imp
#### 5、区别
1. export命令导出的tar文件略小于save命令导出的
2. export命令是从容器（container）中导出tar文件，而save命令则是从镜像（images）中导出
3. 基于第二点，export导出的文件再import回去时，无法保留镜像所有历史（即每一层layer信息，不熟悉的可以去看Dockerfile），不能进行回滚操作；而save是依据镜像来的，所以导入时可以完整保留下每一层layer信息。
#### 6、建议
可以依据具体使用场景来选择命令
1. 若是只想备份images，使用save、load即可
2. 若是在启动容器后，容器内容有变化，需要备份，则使用export、import


