<!-- TOC START min:1 max:3 link:true asterisk:false update:true -->
- [kubernetes安装](#kubernetes安装)
  - [1、背景](#1背景)
  - [2、环境准备](#2环境准备)
    - [1、禁用 swap](#1禁用-swap)
    - [2、检查依赖模组](#2检查依赖模组)
    - [3、修改 sysctl 配置](#3修改-sysctl-配置)
    - [4、关闭防火墙](#4关闭防火墙)
    - [5、用户分组](#5用户分组)
    - [6、配置SSH 密钥](#6配置ssh-密钥)
  - [3、安装](#3安装)
    - [1、下载RKE](#1下载rke)
    - [2、准备集群服务器](#2准备集群服务器)
    - [3、编写集群初始化文件](#3编写集群初始化文件)
    - [4、启动](#4启动)
    - [5、可能遇见的问题](#5可能遇见的问题)
<!-- TOC END -->





# kubernetes安装
---
## 1、背景
使用RKE安装kubernetes集群，Rancher Kubernetes Engine，简称 RKE，是一个经过 CNCF 认证的 Kubernetes 安装程序。RKE 支持多种操作系统，包括 MacOS、Linux 和 Windows，可以在裸金属服务器（BMS）和虚拟服务器（Virtualized Server）上运行。
>RKE官方安装kubernetes文档可参考：
https://docs.rancher.cn/docs/rke/_index/

## 2、环境准备
操作系统：CentOS 7.6 64位  
安装镜像：CentOS-7.6.1810-x86_64.iso  
安装方式为虚拟机最小化安装，配置静态IP,设置桥接网络  
使用用户 root 安装依赖软件  
使用自定义用户 docker 运行集群  
需安装docker
### 1、禁用 swap
>swapoff -a

### 2、检查依赖模组
```Bash
#!/bin/bash
for module in br_netfilter ip6_udp_tunnel ip_set ip_set_hash_ip ip_set_hash_net iptable_filter iptable_nat iptable_mangle iptable_raw nf_conntrack_netlink nf_conntrack nf_conntrack_ipv4   nf_defrag_ipv4 nf_nat nf_nat_ipv4 nf_nat_masquerade_ipv4 nfnetlink udp_tunnel veth vxlan x_tables xt_addrtype xt_conntrack xt_comment xt_mark xt_multiport xt_nat xt_recent xt_set  xt_statistic xt_tcpudp;
    do
      if ! lsmod | grep -q $module; then
        echo "module $module is not present";
      fi;
    done
```
会输出缺失的模组，可按如下安装缺失的模组
```Bash
#!/bin/bash
modprobe ip6_udp_tunnel
modprobe ip_set_hash_ip
modprobe ip_set_hash_net
modprobe veth
modprobe vxlan
modprobe x_tables
modprobe xt_comment
modprobe xt_mark
modprobe xt_multiport
modprobe xt_nat
modprobe xt_recent
modprobe xt_set
modprobe xt_statistic
modprobe xt_tcpudp
```
### 3、修改 sysctl 配置
>vi /etc/sysctl.conf  

>增加内容    
 net.bridge.bridge-nf-call-iptables = 1  

>立即生效  
 sysctl -p

### 4、关闭防火墙

>systemctl stop firewalld.service  
systemctl disable firewalld.service

### 5、用户分组
将docker用户添加到docker分组中
>usermod -aG docker docker

### 6、配置SSH 密钥
>ssh-keygen  

连续按三次 enter 即可生成密钥  
相互复制公钥到其他节点
>ssh-copy-id -i .ssh/id_rsa.pub docker@192.168.0.101

复制完成即可实现免密登录，测试一下
>ssh 192.168.0.101


## 3、安装
### 1、下载RKE
下载地址为：
>https://github.com/rancher/rke/releases  


>使用 wget 下载  
yum install wget  
wget https://github.com/rancher/rke/releases/download/v1.1.9/rke_linux-amd64  

>重命名  
mv rke_linux-amd64 rke

>移动至sbin  
mv rke /usr/sbin/

### 2、准备集群服务器
>192.168.0.101 master  
192.168.0.102 worker  
192.168.0.103 worker  
192.168.0.104 worker

### 3、编写集群初始化文件
> vi cluster.yml

最小化配置
```yaml
nodes:
  - address: 192.168.0.101
    user: docker
    role:
      - controlplane
      - etcd
      - worker
```

### 4、启动
>启动集群  
rke up

>移除集群  
rke remove

>移动集群默认配置文件至 .kube  
mv kube_config_cluster.yml .kube/config

### 5、可能遇见的问题
#### 1、报错1
>Failed to get job complete status for job rke-network-plugin-deploy-job in namespace kube-system  
此问题安装 kubectl 即可解决，安装方法可参考
https://blog.csdn.net/qsz1281509180/article/details/106302494/
