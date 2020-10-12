<!-- TOC titleSize:2 tabSpaces:2 depthFrom:1 depthTo:6 withLinks:1 updateOnSave:1 orderedList:0 skip:0 title:1 charForUnorderedList:* -->
## Table of Contents
* [kubernetes安装](#kubernetes)
  * [1、背景](#1)
  * [2、环境准备](#2)
    * [1、禁用 swap](#1-swap)
    * [2、检查依赖模组](#2)
    * [3、修改 sysctl 配置](#3-sysctl-)
    * [4、关闭防火墙](#4)
    * [5、用户分组](#5)
  * [3、安装](#3)
    * [1、下载RKE](#1rke)
    * [2、准备集群服务器](#2)
    * [3、编写集群初始化文件](#3)
<!-- /TOC -->
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
增加内容    
 net.bridge.bridge-nf-call-iptables = 1  
立即生效  
 sysctl -p
### 4、关闭防火墙
```Bash
systemctl stop firewalld.service
systemctl disable firewalld.service
```
### 5、用户分组
将docker用户添加到docker分组中
>usermod -aG docker docker
## 3、安装
###1、下载RKE
下载地址为：
>https://github.com/rancher/rke/releases  

```
--使用 wget 下载
yum install wget  
wget https://github.com/rancher/rke/releases/download/v1.1.9/rke_linux-amd64  
--重命名  
mv rke_linux-amd64 rke
--移动至sbin  
mv rke /usr/sbin/
```
### 2、准备集群服务器
>192.168.0.101 master  
192.168.0.102 worker  
192.168.0.103 worker  
192.168.0.104 worker
### 3、编写集群初始化文件
