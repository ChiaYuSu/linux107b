# 第十一週筆記
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

## Nagios 介紹
* 它是一套監視服務的軟體。它可以透過網路監視各種服務，像是TCP por、SMT、POP3、HTTP、NNTP、PING 等等
* 它提供一套介面，任何人都可以利用這個介面，為任何特殊的服務開發監視用插件，然後交給 Nagios 運行
* 當所監視的對象（主機或服務）發生問題時，或是問題解決時，它可已經由 email，呼叫器，或是任何設定好的方法，對於相關人士發出通知
* 它可以定義一些事件的處理方法。當問題或是事件發生時，針對問題或是事件的種類進行問題對應或是事件反應
* 它有一個美麗的 Web 畫面和很多便利的功能。你可以觀看現在各種服務運行的狀況，發出了那些通知，問題發生的履歷，以及運行期間的記錄檔
* 它的設計可以讓插件的開發者很有發揮的空間。Nagios 的核心裡包含了所有的功能，是以 C 語言寫成的。但是監視的插件是獨立於核心之外，這些插件可以用任何語言寫成，只要是輸入與輸出的結果能夠照者核心所能認識的格式，插件本身是如何被開發是完全自由的

## 監控端 Server 步驟
1. `yum install epel-release`
2. `yum install httpd nagios* nrpe`
3. `systemctl start httpd`
4. `systemctl start nagios`
5. `htpasswd -c /etc/nagios/passwd nagiosadmin`（自行設定密碼：centos）
6. `systemctl restart httpd`
7. `systemctl restart nagios`
8. `cd /etc/nagios/projects`
9. `gedit localhost.cfg`
10. 新增 define host (web1)
11. `systemctl restart nagios`

## 被控端 Server 步驟
1. `yum install epel-release nrpe nagios-plugin*`
2. `gedit /etc/nagios/nrpe.cfg`
3. allow host 新增監控端 server IP
4. `systemctl restart nrpe`

## 延伸學習
1. []()