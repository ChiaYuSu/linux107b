# 第九週
## 
* `ipvsadm -C`：清除規則
* `ipvsadm -Ln`：
* `ipvsadm -Lnc`： 

## Keepalived Solution 設定檔
* http://www.linuxvirtualserver.org/docs/ha/keepalived.html

## 不同平台下載指令
* CentOS：`yum install`
* Ubuntu：`apt-get install`

## 五台虛擬機網卡設定
* LVS-1 (router-1、vm-1)：NAT、intnet、Host Only
* LVS-2 (router-2、vm-2)：NAT、intnet、Host Only
* Client：Host Only、intnet
* CentOS 7.0-1 (web-1)：intnet
* CentOS 7.0-2 (web-2)：intnet

## 延伸學習
1. [Linux运维日志 centos.bz/LVS Keepalived双机高可用负载均衡搭建](https://www.centos.bz/2017/07/lvs-keepalived-ha-loadbalace/)
2. [Linux运维日志 centos.bz/lvs详细介绍及lvs和keepalived的使用](https://www.centos.bz/2017/09/lvs-intro-and-lvs-keepalived/)