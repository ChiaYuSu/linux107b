# 第十六週筆記

## 本週主要上課內容
* 補上上週（Week 14）老師因家中有事請假的課，因此今天晚上 18:30~21:00 需補課
* ansible

## 環境配置
* 主控端（Client）：NAT + Host Only
    * 筆者 ip：192.168.56.102
* 被控端 1（Server 1）：NAT + Host Only
    * 筆者 ip：192.168.56.103
* 被控端 2（Server 2）：NAT + Host Only
    * 筆者 ip：192.168.56.107

## 前置作業
1. 三台機器都安裝 ssh：`yum install ssh`
2. 開啟 ssh：`systemctl start sshd`
3. 檢查 ssh 是否被開啟：`netstat -tunlp | grep 22`
4. 生成 ssh 公開金鑰：`ssh-keygen`
5. 切換目錄：`cd /root/.ssh`
6. 把主控端公開金鑰複製到遠端（被控端）主機：`scp id_rsa.pub user@192.168.56.103:/tmp/authorized_keys`、`scp id_rsa.pub user@192.168.56.107:/tmp/authorized_keys`
7. 確認被控端主機是否有連上：`ssh 192.168.56.103`、`ssh 192.168.56.107`
    ```
    Last login: Wed Jun  5 10:35:35 2019 192.168.56.102
    ```

## ansible 安裝及應用（僅需在主控端安裝即可）
1. 首先需要安裝 epel-release：`yum install epel-release`
2. 接著繼續安裝 ansible：`yum install ansible -y`
3. 在主控端配置 ansible.cfg 文件：`gedit /etc/ansible/hosts`（配置主機清單）
    ```
    [app1]
    192.168.56.103

    [app2]
    192.168.56.107

    [myapp]
    192.168.56.103
    192.168.56.107
    ```

4. 查看遠端（被控端）指令：`ansible app1 -m command -a "ls /root"`

## ansible 的用法
* ansible 不是和大型管理環境，所有的管理都是透過 ssh 實現配置管理、應用部署、任務執行等功能
* ansible 語法：`ansible <host-pattern> [選項] [-a args]`
* 選項及說明

    | 選項 | 說明 |
    | ---- | ---- |
    | -m module | 指定模組，默認是 command（另外上課有講到 command、shell、script、copy、fetch、file、yum、service、user、group等模組） |
    | --list-hosts | 顯示主機列表 |

## command 模組
* 預設的模組，執行指定的命令
    * 缺點：只能支援基本的指令，如出現萬用字元不支援，所以如果要採用較進階的指令，會建議使用 `shell 模組`
* 檢查是否安裝 `vsftpd` 套件，可以輸入 `ansible app1 -m command -a "rpm -q vsftpd"`
* 查看使用者（user）是否存在，可以輸入 `ansible app1 -m command -a "getent passwd user"`（以 X 隱藏密碼）；若需要查看加密後的密碼，可以輸入 `ansible app1 -m command -a "getent shadow user"`

## shell 模組應用
* 相對於 `command` 指令，較進階的指令建議使用 `shell` 指令
* creates 參數：當檔案存在時**不執行**後面的指令，可以輸入 `ansible app1 -m shell -a "creates=/tmp/test ls /tmp"`
    * 若檔案不存在執行 `ls /tmp`，結果如下
        ```
        192.168.56.103 | CHANGED | rc=0 >>
        ansible_command_payload_iwqk5b
        ssh-F39oTT9cpIGU
        systemd-private-a3098911bc6f4adf840e4651af89abb1-bolt.service-XayddU
        systemd-private-a3098911bc6f4adf840e4651af89abb1-chronyd.service-nczl9E
        ...
        ```
    * 若檔案存在則跳過後面指令，結果如下
        ```
        192.168.56.103 | SUCCESS | rc=0 >>
        skipped, since /tmp/test exists
        ```
* removes 參數：當檔案存在時**執行**後面的指令，可以輸入 `ansible app1 -m shell -a "removes=/tmp/test ls /tmp"`
    * 若檔案不存在跳過後面指令，結果如下
        ```
        192.168.56.103 | SUCCESS | rc=0 >>
        skipped, since /tmp/test does not exist
        ```
    * 若檔案存在則執行後面指令 `ls /tmp`，結果如下
        ```
        192.168.56.103 | CHANGED | rc=0 >>
        ansible_command_payload_iwqk5b
        ssh-F39oTT9cpIGU
        systemd-private-a3098911bc6f4adf840e4651af89abb1-bolt.service-XayddU
        systemd-private-a3098911bc6f4adf840e4651af89abb1-chronyd.service-nczl9E
        ...
        ```

## script 模組應用
* 在主控端**寫一個腳本**並在被控端運行
1. 先在主控端寫一個腳本，並將腳本寫入 `a.sh`，指令為 `vim a.sh`
    * 腳本如下
        ```
        #!/usr/bin/bash

        echo "hello world"
        hostname
        ```
2. 再次檢查主控端是否有寫入，可輸入 `cat ./a.sh`
3. 接著輸入 `ansible app1 -m script -a a.sh` 測試執行，查看被控端是否回傳 `a.sh` 腳本內容
    * 執行結果如下
        ```
        192.168.56.103 | CHANGED => {
            "changed": true,
            "rc": 0,
            "stderr": "Shared connection to 192.168.56.103 closed.\r\n",
            "stderr_lines": [
                "Shared connection to 192.168.56.103 closed."
            ],
            "stdout": "hi\r\nlocalhost.localdomain\r\n",
            "stdout_lines": [
                "hi",
                "localhost.localdomain"
            ]
        }
        ```

## copy 模組應用
* 將主控端檔案**複製**至被控端
1. 在主控端 root 根目錄下建立新檔案 a.txt：`echo "hi" > a.txt`
2. 把 a.txt 拷貝至被控端並做備份（因為有可能檔名是一樣的）：`ansible app1 -m copy -a "src=/root/a.txt dest=/tmp/a.txt backup=yes"`
3. 接著可利用 `command` 模組輸入 `ansible app1 -m command -a "ls /tmp"` 到被控端 tmp 目錄下檢查是否有成功將檔案拷貝過去

## fetch 模組應用
* 將被控端檔案**擷取**至主控端，一般經常將被控段的 `log` 記錄檔擷取至主控端
1. 假設我想查台看兩被控端所有的使用者資訊，我可以輸入 `ansible myapp -m fetch -a "src=/etc/passwd dest=/tmp"`，這個指令代表會從被控端擷取 `/etc/passwd` 使用者資料到主控端 `/tmp` 目錄下
    * 若成功會出現
        ```
        192.168.56.103 | SUCCESS => {
            "changed": false,
            "checksum": "1ab8e7ca203423162e16fd322a753ce29625c07a",
            "dest": "/tmp/192.168.56.103/etc/passwd",
            "file": "/etc/passwd",
            "md5sum": "ff738ecc1eb50551b84b3439e6271a16"
        }
        192.168.56.107 | SUCCESS => {
            "changed": false,
            "checksum": "5a3fa1b1e83d86b5e0b2fa967a87fda8e417981d",
            "dest": "/tmp/192.168.56.107/etc/passwd",
            "file": "/etc/passwd",
            "md5sum": "d415648d6c66501e6cc6c396e0b9fc23"
        }
        ```
2. 接著我們可以在主控端輸入 `cd /tmp` 切換目錄至 `tmp` 下，再輸入 `ls`，查詢是否有成功將被控端 `passwd` 檔案 fetch 回來。這裡要注意的是由於我們會管理大量的伺服器，然後不同的伺服器可能有相同的檔案，因此 ansible 在 fetch 回來的檔案會根據伺服器 ip 自動產生相對應的目錄（ip 位址就是目錄名稱）
    * 執行結果
        ```
        192.168.56.103 
        192.168.56.107
        ssh-9WGR1yPmGi1L
        systemd-private-db90827786894542ab67f22fefc9a383-bolt.service-MyDHo3
        systemd-private-db90827786894542ab67f22fefc9a383-chronyd.service-MuOlt0
        systemd-private-db90827786894542ab67f22fefc9a383-colord.service-kH0ifu
        systemd-private-db90827786894542ab67f22fefc9a383-rtkit-daemon.service-AvcvNQ
        Temp-51de5a8c-3c05-4f87-8f99-0ee9e14b856e
        tmp.Oyo4zVkGzn
        tracker-extract-files.1000
        ```

## file 模組應用

## yum 模組應用
* 在遠端（被控端）**安裝 / 解除安裝**套件
1. 在主控端輸入 `ansible app1 -m command -a "rpm -q vsftpd"` 檢查是否有安裝過指定套件（這裡筆者是以 ftp 套件當作範例）
    * 若有安裝過會出現
        ```
        192.168.56.103 | CHANGED | rc=0 >>
        vsftpd-3.0.2-25.el7.x86_64
        ```
    * 若沒有安裝過會出現
        ```
        192.168.56.103 | FAILED | rc=1 >>
        package vsftpd is not installednon-zero return code
        ```
2. 若要安裝 ftp 套件輸入：`ansible app1 -m yum -a "name=vsftpd state=present"`（這裡的 `state=present` 可省略，因為預設狀態就是 present）

3. 若要解除安裝 ftp 套件則可輸入：`ansible app1 -m yum -a "name=vsftpd state=absent"`

## service 模組應用
* **啟動 / 關閉 / 重新啟動 / 重新載入**遠端（被控端）**伺服器**
1. 檢查被控端主機是否有開啟 ssh 伺服器：`ansible app1 -m command -a "netstat -tunlp | grep 22"`
    * 若有開啟會出現
        ```
        192.168.56.103 | CHANGED | rc=0 >>
        Active Internet connections (only servers)
        Proto Recv-Q Send-Q Local Address       	Foreign Address     	State   	PID/Program name       	 
        tcp    	0  	0 0.0.0.0:22          	0.0.0.0:*           	LISTEN  	4564/sshd
        ```
2. 若需要關閉 ssh 伺服器：`ansible app1 -m service -a "name=sshd state=stopped"`
    * state 選項還有：`started`（啟動）、`restarted`（重新啟動）、`reloaded`（重新載入）可供使用
3. 再次檢查被控端主機是否關閉 ssh 伺服器：`ansible app1 -m command -a "netstat -tunlp | grep 22"`

## user 模組應用
* **新增 / 刪除**遠端（被控端）**使用者**
1. 檢查被控端主機是否有此使用者（user1）：`ansible app1 -m command -a "getent passwd user1"`
    * 若無會出現
        ```
        192.168.56.103 | FAILED | rc=2 >>
        non-zero return code
        ```
2. 若無新增使用者（user1）：`ansible app1 -m user -a 'name=user1'`
3. 接著檢查使用者（user1）是否成功在被控端被新增：`ansible app1 -m command -a "getent passwd user1"`
    * 若有會出現
        ```
        192.168.56.103 | CHANGED | rc=0 >>
        user1:x:1001:1001::/home/user1:/bin/bash
        ```
4. 若要刪除使用者（user1）輸入：`ansible app1 -m user -a 'name=user1 state=absent'`

## group 模組應用
* **新增 / 刪除**遠端（被控端）**使用者群組**
1. 檢查被控端主機是否有此群組（aaa）：`ansible app1 -m command -a "getent group aaa"`
    * 若無會出現
        ```
        192.168.56.103 | FAILED | rc=2 >>
        non-zero return code
        ```
2. 若無新增群組（aaa）：`ansible app1 -m group -a 'name=aaa'`
3. 接著檢查群組（aaa）是否成功在被控端被新增：`ansible app1 -m command -a "getent group aaa"`
    * 若有會出現
        ```
        192.168.56.103 | CHANGED | rc=0 >>
        aaa:x:1002:
        ```
4. 若要刪除使用者（user1）輸入：`ansible app1 -m group -a 'name=aaa state=absent'` 

## playbook 腳本
1. 
2. continue...
    1. 在主控端寫腳本（test.yml）：`vim test.yml`
        * 腳本內容如下：
            ```
            ---
            - hosts: app1
            remote_user: root
            tasks:
                - name: create new file
                file: name=/tmp/newfile state=touch
                - name: create new user
                user: name=test1 system=yes shell=/sbin/nologin
                - name: install package
                yum: name=httpd
                - name: copy html
                copy: src=a.html dest=/var/www/html
                - name: start service
                service: name=httpd state=started
            ```
    2. 再寫 a.html 的配置檔：


## 延伸學習
1. [51CTO博客 / ansible从入门到放弃](https://blog.51cto.com/11886307/2385720)
2. [Github / 林柏億 istar0me](https://github.com/istar0me/linux-note/blob/107-2/W16%2020190605.md#ansible-playbooks)
3. [Youtube / IT边看边学](https://www.youtube.com/watch?v=4lIi8d6m27c&list=PLOfJyn_hHwP_ATbd7GkDobQUiA2i7jLhN)

