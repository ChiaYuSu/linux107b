# 第十三週筆記
* 老師抱歉，本週睡過頭，晚一節課才到

## rsync 與 inotify
* rsync：遠端備份
* inotify：即時檔案監控系統，當檔案新增、修改、刪除時會發出通知

### 使用時機
* 立即還原：若今天伺服器遭駭，首頁被更改。能透過 inotify 發出通知，並且搭配 rsync Pull 遠端伺服器資料還原先前的資料
* 立即備份：請參閱撰寫腳本，透過 rsync + inotify 來自動同步檔案

## rsync
* 英文全名：reomte synchronize
* 用途：遠端同步工具
* 特點：   
    * 支援雙向操作：可進行 Push 與 Pull
    * 採用增量備份（Incremental Backup），而非完全備份（Full Backup），因此第一次後的備份速度更快且節省頻寬
* Client 端 vs. Server 端
    * Client 端與 Server 端主機都需要安裝 rsync
    * Server 端主機的防火牆需要開放 873 port，可透過 iptables 設定（或可以直接關閉防火牆）
    * Server 端主機需要設定 `/etc/rsyncd.conf` 與 `/etc/rsyncd.secrets`
    * Client 端主機要設定 `/etc/rsyncd.secrets`

## pull
* Client 端從 Server 端拉取資料
* 開始之前請先關閉 SELinux 與防火牆
    * selinux 關閉方式：`vi /etc/sysconfig/selinux` -> 找到 `SELINUX=enforcing` 將其改成 `SELINUX=disabled` -> 重機啟動虛擬機 `reboot`
    * 防火牆關閉方式：`systemctl stop firewalld` 

### Server 端
1. `yum install -y rsync`：安裝 rsync
2. 配置 rsync 文件，輸入 `vim /etc/esyncd.conf`
    * 配置檔如下
        ```
        uid = root
        gid = root
        use chroot = yes # 改變程序執行時參考的根目錄位置（安全性考量，避免其他路徑的檔案被修改）
        max connections = 10
        pid file = /var/run/rsyncd.pid
        log file = /var/log/rsync.log # 傳輸紀錄文件位置
        transfer logging = yes # 是否留下傳輸紀錄的文件
        timeout = 900
        ignore nonreadable = yes
        dont compress = *.gz *.tgz *.zip *.z *.Z *.rpm *.deb *.bz2
        [mybackup] # 模組名稱
        path = /data # 要備份的路徑
        secrets file = /etc/rsyncd.pass # rsync 帳密
        ```
3. 新增 rsync 帳密檔案，輸入 `vim /etc/rsyncd.pass`
    * 配置檔如下
        ```
        backup:centos # 帳號:密碼
        ```
4. 變更帳密檔案權限，有讀寫權限，輸入 `chmod 600 /etc/rsyncd.pass`
5. 啟動 rsync 伺服器，輸入 `systemctl start rsyncd`

### CLient 端
1. `yum install -y rsync`：安裝 rsync
2. 新增 rsync 帳密檔案，這裡只先需要設定密碼，帳號後面會設定，輸入 `vim /etc/rsyncd.pass`
    * 配置檔如下
        ```
        centos
        ```
3. 變更帳密檔案權限，有讀寫權限，輸入 `chmod 600 /etc/rsyncd.pass`
4. pull 測試，輸入 `rsync -vzrtopg --delete --progress --password-file=/etc/rsyncd.pass backup@192.168.56.102::mybackup /backup/192.168.56.102`
    * 選項補充
        * `-v`：顯示詳細輸出
        * `-z`：傳輸時壓縮檔案
        * `-r`：
        * `-t`：保留時間訊息
        * `-o`：保留擁有者訊息
        * `-p`：保留權限訊息
        * `-g`：保留群組訊息
        * `--progress`：顯示進度條
        * `--password-file=FILE`：指定密碼檔案

## push
* Server 端推送資料給 Client 端
1. 讓 Server 端連 Client 端不用再輸入密碼
2. push 測試，輸入 `rsync -avz --delete /data root@192.168.56.104:/data/192.168.56.102`

## inotify
1. 無法透過 `yum` 指令來安裝，需要手動下載
```
wget http://jensd.be/download/inotify-tools-3.14.tar.gz # 原始碼下載
tar -xvfz inotify-tools-3.14.tar.gz # 解壓縮
cd inotify-tools-3.14
./configure # 組態設定
make # 編譯
make install # 安裝至系統目錄
```
2. 監控 `/data` 路徑中的行為（新增、修改、刪除等），輸入 `inotifywait -mrq --timefmt '%d/%m/%y/%H:%M' --format '%T %w %f' -e modify,delete,create,attrib /data`
    * 選項補充
        * `-e modify,delete,create,attrib`：說明要監控的行為
3. 再開啟一個 terminal，在 `/data` 中新增檔案，檢查執行 `inotify` 的視窗有沒有新訊息，輸入 `cd /data` -> `touch test`

## 撰寫腳本
* 透過透過 rsync 搭配 inotify 來自動同步檔案
1. 在 Client 端新增一個腳本檔 `/rsync.sh`
    * 腳本檔如下
        ```
        #!/usr/bin/bash

        /usr/local/bin/inotifywait -mrq --timefmt '%Y/%m/%d-%H:%M:%S' --format '%T %w %f' \
        -e modify,delete,create,move,attrib /data/backup \
        | while read file
            do
            /usr/bin/rsync -auvrtzopgP --delete progress /data root@192.168.56.104:/data/192.168.56.102
        done
        ```

2. 新增腳本檔執行權限，然後執行，輸入 `chmod u+x /rsync.sh` -> `/rsync.sh`

3. Server 端新增、刪除檔案來測試是否有同步，輸入 `touch f{1..5}` -> `rm -rf f {1..5}`


## 延伸學習
1. [Linux 技術手札 / CentOS 7 關閉防火牆及 SELinux](https://www.opencli.com/linux/centos-7-disable-firewalld-selinux)