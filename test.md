### 1、导入与导出镜像
涉及的命令有export、import、save、load
####1、使用save保存镜像至文件
docker save -o nginx.tar nginx:latest
或者
docker save > nginx.tar nginx:latest
2、导入镜像文件
docker load -i nginx.tar
或
docker load < nginx.tar


