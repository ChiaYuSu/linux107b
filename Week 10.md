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
* LVS-1 (router-1、vm-1)：NAT、Host Only
* LVS-2 (router-2、vm-2)：NAT、Host Only

## 延伸學習
1. []()