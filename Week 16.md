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
7. 確認被控端主機是否有連上：`ssh 192.168.56.103`、`ssh 192.168.56.106`
    ```
    Last login: Wed Jun  5 10:35:35 2019 192.168.56.102
    ```
    
## ansible 安裝及應用
1. 首先需要安裝 epel-release：`yum install epel-release`
2. 安裝 ansible：`yum install ansible -y`
3. 在主控端配置 ansible.cfg 文件：`gedit /etc/ansible/hosts`（配置主機清單）
    ```
    [app1]
    192.168.56.103

    [app2]
    192.168.56.106

    [myapp]
    192.168.56.103
    192.168.56.106
    ```

4. 查看遠端（被控端）指令：`ansible app1 -m command -a "ls /root"`

## ansible 的用法
* ansible 不是和大型管理環境，所有的管理都是透過 ssh 實現配置管理、應用部署、任務執行等功能
* ansible 語法：`ansible <host-pattern> [選項] [-a args]`
* 選項及說明

    | 選項 | 說明 |
    | ---- | ---- |
    | -m module | 指定模塊，默認是 command（另外上課有講到 command、shell、script、copy、fetch等模塊） |
    | --list-hosts | 顯示主機列表 |

## ansible shell 模塊應用

## ansible script 模塊應用

## ansible copy 模塊應用
1. 在主控端 root 根目錄下建立新檔案 a.txt：`echo "hi" > a.txt`
2. 把 a.txt 拷貝至被控端（192.168.56.103）並做備份（因為有可能檔名是一樣的）：`ansible app1 -m copy -a "src=/root/a.txt dest=/tmp/a.txt backup=yes"`
3. 到被控端 tmp 目錄下檢查（192.168.56.103）是否有成功將檔案拷貝過去：`cd /tmp` -> `ls`

## ansible fetch 模塊應用

## ansible file 模塊應用

## ansible yum 模塊應用
1. 在主控端輸入 `ansible app1 -m command -a "rpm "`

## ansible service 模塊應用
1. 

## ansible user 模塊應用
* 新增 / 刪除使用者
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

## ansible group 模塊應用
1. 

## ansible playbook 腳本
1. 
2. 
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

