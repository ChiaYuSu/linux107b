# 期末報告
## 主題
* Saltstack

## Saltstack 是什麼
* Saltstack 是一個服務器基礎架構**集中化管理平臺**，具備配置管理、遠程執行、監控等功能，通過部署 Saltstack 環境，我們可以在**成千上萬臺服務器**上做到批量執行命令，根據不同業務特性進行配置集中化管理、分發文件、采集服務器數據、操作系統基礎等

## Saltstack vs. Ansible vs. Puppet vs. Chef
![](https://github.com/ChiaYuSu/linux107b/blob/master/project/compare.PNG)

## 環境架設
* 準備兩台以上的機器（筆者測試環境為 3 台）
    * Client: CentOS（NAT、HOST ONLY）
        * IP: 192.168.56.102
    * Server-1: CentOS（NAT、HOST ONLY）
        * IP: 192.168.56.103
    * Server-2: Ubuntu（NAT、HOST ONLY）
        * IP: 192.168.56.108

### Client 端
1. 安裝 `epel-release` 套件：`yum install epel-release`
2. 安裝 `salt-master` 及 `salt-minion` 套件：`yum install salt-msater salt-minion`
3. 關閉防火牆：`systemctl disable firewalld`（檢查防火牆是否為關閉狀態：`systemctl status firewalld`）
    > 也可以選擇加入防火牆例外規則 `sudo firewall-cmd --add-port=4505/tcp --permanent`、`sudo firewall-cmd --add-port=4506/tcp --permanent`
4. 檢查 4505 port 及 4506 port 是否為 LISTEN 狀態：`netstat -tunlp | grep -e 4505 -e 4606`
    > 4505 port：設置 master 與 minion 的認證通信端口<br>
    > 4506 port：master 用來發送訊息或者接受 minion 傳回的訊息
5. 啟動 `salt-master` 套件：`systemctl start salt-master`
    > 若想要讓套件下次開機就自動啟動可以再輸入此指令：`systemctl enable salt-master`
6. 更改 `salt-master` 配置檔：`gedit /etc/salt/master`
    > `interface: 0.0.0.0`<br>
    > `auto-accept: True`（選擇性設定）
7. 更改 `salt-minion` 配置檔：`gedit /etc/salt/minion`
    > `master: master`

### Server-1 端
1. 前 3 點同上
2. 前 3 點同上
3. 前 3 點同上
4. 檢查 4505 port 及 4506 port 是否為 ESTABLISHED 狀態：`netstat -atunlp | grep -e 4505 -e 4506`
    > ESRABLISHED：port 代表連線已建立，準備好接收及發送數據到遠端節點
5. 啟動 `salt-minion` 套件：`systemctl start salt-minion`
    > 若想要讓套件下次開機就自動啟動可以再輸入此指令：`systemctl enable salt-minion`
6. 更改 `salt-minion` 配置檔：`gedit /etc/salt/minion`
    > `master: 192.168.56.102

### Server-2 端
* 環境配置方式類似 Server-1 端，僅是系統不同而已但指令大同小異
* 以上配置完成沒問題，就請大家回到第一台機器準備做**配置認證**以及**測試**啦！

## 環境配置認證及測試
### Client 端
1. 在接受 server 端主機之前可以先查看一下主機配置狀況：`salt-key -L`
    * 在 client 端還沒接受之前顯示如下：
        ```
        [root@localhost salt]# salt-key
        Accepted Keys:
        Denied Keys:
        Unaccepted Keys:
        server-1
        server-2
        Rejected Keys:
        ```
2. 接下來接受所有主機設定：`salt-key -A`，設定完後可再次查看主機配置狀況：`salt-key -L`
    * 設定完之後顯示如下：
        ```
        [root@localhost salt]# salt-key
        Accepted Keys:
        server-1
        server-2
        Denied Keys:
        Unaccepted Keys:
        Rejected Keys:
        ```
        > 補充，選項還有以下幾種：<br>
        `salt-key -a server-1`：接受 `server-1` 主機<br>
        `salt-key -A`：接受所有主機<br>
        `salt-key -d server-2`：刪除 `server-2` 主機<br>
        `salt-key -D`：刪除所有主機
3. 當兩台 server 端主機都成功被接受就可以開始測試啦，我們可以輸入 `salt '*' test.ping` 測試是否有配置成功
    * 成功配置結果如下：
        ```
        [root@localhost salt]# salt '*' test.ping
        server-1:
            True
        server-2:
            True
        ```

## 上課有講到功能
### copy
* 將檔案從 master 端拷貝到 minion 端
1. 首先，在 master 的 `/tmp` 目錄下建立一個 `test.txt` 的檔案，指令為 `cd /tmp` -> `echo "hello world" > test.txt`
2. 將 master `test.txt` 拷貝至 minion 的 `/tmp` 目錄下，指令為 `salt-cp '*' /tmp/test.txt /tmp`
    > 這裡需要注意 **master 的路徑前面**跟 **minion 的路徑後面**不能加引號（""） 
3. 完成後確認 minion 端是否真的有此檔案，指令為 `salt '*' cmd.run "cat /tmp/test.txt"`
> [Demo 影片](https://drive.google.com/open?id=1AIWK501iWae4kXmhWcseQ-GKGqiS5hhc)

### remove
* 刪除 minion 端檔案
1. 假設今天需要刪除 minion 端 `/tmp` 目錄下的 `test.txt`，指令為 `salt '*' cmd.run "rm /tmp/test.txt"`
2. 若要確認檔案（`test.txt`）有被刪除，可以輸入 `salt '*' cmd.run "/tmp | grep test"`
> [Demo 影片](https://drive.google.com/open?id=1STPij9DA5_-KK0HBv9_TQ3RH6NvJQ75n)

### yum
* 在 minion 端安裝套件
1. 假設今天我想在 minion 端安裝 `joe` 套件，指令為 `salt '*' cmd.run "yum install joe -y"`
2. 安裝完後若想要確認是否有成功安裝，可以輸入 `salt '*' cmd.run "rpm -q joe"`
    * 若有成功安裝，結果如下
        ```
        client:
            joe-4.6-4.el7.x86_64
        ```
    * 若無成功安裝，結果如下
        ```
        client:
            package joe is not installed
        ERROR: Minions returned with non-zero exit mode
        ```
3. 安裝完套件後若要卸載可輸入 `salt '*' cmd.run "rpm -e joe"`
4. 若要確認是否有成功卸載套件，指令承第 2 步驟
> [Demo 影片](https://drive.google.com/open?id=1JnWpfQwdDhcHE2n0fWIkxYhUM9h2H_G0)

### user
* 在 minion 端增加 / 刪除 / 更改使用者
1. 假設今天要在 minion 端新增一個名為 `test` 的使用者，指令為 `salt '*' user.add "name=test"`
2. 若想要確認使用者是否被成功新增，可以輸入 `salt '*' user.getent | grep test`、若需要看到詳細資料（gid、groups、uid 等）需輸入 `salt '*' user.getent`
3. 若要刪除使用者可以輸入 `salt '*' user.delete "name=test"`
4. 若要更改使用者名稱可以輸入 `salt '*' user.rename test test2`
    > 這裡需要注意**舊的 name 前面**跟**新的 name 後面**不能加引號（""） 
> 註記：user.add 可添加的參數還有 `uid`、`gid`、`groups`、`home`、`shell`、`unique`、`system`、`fullname`、`roomnumber`、`workphone`、`homephone`、`other`、`createhome`、`loginclass`、`root`、`nologinit`
> [Demo 影片](https://drive.google.com/open?id=1TVWysxYfqycgXtI0dr0kVFRPut4jwMCd)

## 補充功能
### manage
* 查看 minion 端機器狀態（開啟 -- up 或關閉 -- down）
1. 查看有哪些機器是開啟的，指令為 `salt-run manage.up`
2. 查看有哪些機器是關閉的，指令為 `salt-run manage.down`
3. 同時查看兩種狀態，指令為 `salt-run manage.status`
> [Demo 影片](https://drive.google.com/open?id=1i-UhoViDDQpHGevJR6yfeAyW2PeEhirb)

### grains
* 查看 minion 端機器是用什麼系統（OS）
1. 查看所有 minion 端機器的系統，指令為 `salt '*' grains.item os`
> [Demo 影片](https://drive.google.com/open?id=1mx8TvmeHb3pndrGcwtvluFjwAlCY4vzq)

### system 
* 切換 minion 端機器狀態
1. 將所有 minion 端機器狀態改成**文字化**介面，指令為 `salt '*' system.init 3`
2. 將所有 minion 端機器狀態改成**圖形化**介面，指令為 `salt '*' system.init 5`
3. 將所有 minion 端機器重新啟動，指令為 `salt '*' system.reboot` 
4. 將所有 minion 端機器關機，指令為 `salt '*' system.poweroff` 
> [Demo 影片](https://drive.google.com/open?id=17CDqEyhgPC3RT2Nagqv9XQYz0wW5njWQ)

### call
* 查看 minion 端機器狀態
> 注意：此指令只能在 minion 端機器上單獨操作，不可在 master 端機器上操作
1. 在 minion 端機器上測試 salt-minion 是否正常，指令為 `salt-call "test.ping"`
2. 另外還可以在做個簡單測試，確認是否 salt-minion 有正常運作，指令為 `salt-call "ifconfig"`
> [Demo 影片](https://drive.google.com/open?id=1pSnwLZCZcRRYhr6ZSiYn21cALpo9Fn1N)


## 簡報參考
* [Saltstack 期末報告](https://drive.google.com/open?id=1IhvsjYjrs7-ePL-La_P9rcQAjdg67VOU)

## 延伸學習
1. [SALTSTACK / EXECUTION MODULES](https://docs.saltstack.com/en/latest/ref/modules/all/index.html)
2. [Jiang Jun / Salt (1) 入门](http://ohmystack.com/articles/salt-1-basic)
3. [CSDN wjacketcn / salt常用命令、模块、执行](https://blog.csdn.net/wjacketcn/article/details/50853604)
4. [S小魚仔S Centos 7 SaltStack 自動化運維工具](http://my-fish-it.blogspot.com/2017/08/ss-centos-7-saltstack.html)
5. [ITREAD<sup>01</sup> / 自動化運維工具之SaltStack-1、SaltStack介紹及安裝](https://www.itread01.com/content/1516005485.html)
6. [ITREAD<sup>01</sup> / saltstack介紹](https://www.itread01.com/content/1502442243.html)
7. [雪花新聞 OpenSkill / 部署管理工具：Chef vs Puppet vs Ansible vs SaltStack vs Fabric](https://www.xuehua.us/2018/06/30/%E9%83%A8%E7%BD%B2%E7%AE%A1%E7%90%86%E5%B7%A5%E5%85%B7%EF%BC%9Achef-vs-puppet-vs-ansible-vs-saltstack-vs-fabric/zh-tw/)
8. [运维之路 / SaltStack介绍和架构解析](http://www.361way.com/saltstack-framework/4918.html)
9. [学时网 water / 一、关于salt简介，安装和基本配置](http://www.xuetimes.com/archives/750)
10. [minseo / Saltstack自动化运维](https://www.cnblogs.com/minseo/p/6816042.html)
11. [台部落 zclinux_/ 【Linux】saltstack的使用詳解 超詳細](https://www.twblogs.net/a/5b7ae9de2b7177392c97165d)
