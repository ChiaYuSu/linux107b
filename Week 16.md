# 第十六週筆記

## 本週主要上課內容
* ansible

## 環境配置
* 主控端：NAT + Host Only
    * 筆者 ip：192.168.56.102
* 被控端 1：NAT + Host Only
    * 筆者 ip：192.168.56.103
* 被控端 2：NAT + Host Only
    * 筆者 ip：192.168.56.106

## 前置作業
1. 三台機器都安裝 ssh：`yum install ssh`
2. 開啟 ssh：`systemctl start sshd`
3. 檢查 ssh 是否被開啟：`netstat -tunlp | grep 22`
4. 生成 ssh 公開金鑰：`ssh-keygen`
5. 切換目錄：`cd /root/.ssh`
6. 把主控端公開金鑰複製到遠端（被控端）主機：`scp id_rsa.pub user@192.168.56.103:/tmp/authorized_keys`、`scp id_rsa.pub user@192.168.56.106:/tmp/authorized_keys`
7. 確認被控端主機是否有連上：`ssh 192.168.56.103`、`192.168.56.106`
> Last login: Wed Jun  5 10:35:35 2019 192.168.56.102

## 

