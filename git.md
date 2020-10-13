<!-- TOC START min:1 max:3 link:true asterisk:false update:true -->
- [Git常用命令](#git常用命令)
  - [1、更新本地的远程分支列表](#1更新本地的远程分支列表)
  - [2、放弃本地修改，强制使用远程最新master覆盖本地](#2放弃本地修改强制使用远程最新master覆盖本地)
<!-- TOC END -->



# Git常用命令
## 1、更新本地的远程分支列表  
>git remote update origin --prune


## 2、放弃本地修改，强制使用远程最新master覆盖本地  
>git fetch --all  
git reset --hard origin/master
