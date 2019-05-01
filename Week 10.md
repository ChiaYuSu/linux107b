# 第十週筆記
## 上週回顧
* 有空的同學把上週的課程，自己回家做一下

## 本週課程
* 系統監控：nagios、zabbix
* 上課內容：**nagios**（可監控 linux 系統或 windows 系統）

## Nagios 
* 分為**直接監控**以及**間接監控**
* 直接監控：主機是否 alive，server 是否有成功運行，不需要安裝任何套件在被控端，但只能做一些比較簡單的事情
* 間接監控：可安裝套件在被控端（linux 平台上需安裝 nrpe 套件，若是在 windows 平台上則是安裝 nr c++ 套件），可以做較複雜的事情

## 兩台虛擬機網卡設定
* 監控端 Server（Monitoring）：NAT、Host Only
* 被控端 Server：NAT、Host Only

## 監控端 Server 步驟
1. `yum install epel-release`
2. `yum install httpd nagios* nrpe`
3. `systemctl start httpd`
4. `systemctl start nagios`
5. `htpasswd -c /etc/nagios/passwd nagiosadmin`（自行設定密碼：centos）
6. `systemctl restart httpd`
7. `systemctl restart nagios`

## 被控端 Server 步驟

## 延伸學習
1. []()