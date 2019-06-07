# 期末報告
## 主題
* Saltstack

## Saltstack 是什麼

## Saltstack vs. Ansible vs. Puppet vs. Chef

## 環境架設

## 上課有講到功能
### copy
* 將檔案從 master 端拷貝到 minion 端
1. 首先，在 master 的 `/tmp` 目錄下建立一個 `test.txt` 的檔案，指令為 `cd /tmp` -> `echo "hello world" > test.txt`
2. 將 master `test.txt` 拷貝至 minion 的 `/tmp` 目錄下，指令為 `salt-cp 'client' /tmp/test.txt /tmp`
3. 完成後確認 minion 端是否真的有此檔案，指令為 `salt 'client' cmd.run "cat /tmp/test.txt"`


### remove
* 刪除 minion 端檔案
1. 假設今天需要刪除 minion 端 `/tmp` 目錄下的 `test.txt`，指令為 `salt 'client' cmd.run "rm /tmp/test.txt"`
2. 若要確認檔案（`test.txt`）有被刪除，可以輸入 `salt 'client' cmd.run "/tmp | grep test"`

### fetch

### yum
* 在 minion 端安裝套件
1. 假設今天我想在 minion 端安裝 `joe` 套件，指令為 `salt 'client' cmd.run "yum install joe -y"`
2. 安裝完後若想要確認是否有成功安裝，可以輸入 `salt 'client' cmd.run "rpm -q joe"`
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
3. 安裝完套件後若要卸載可輸入 `salt 'client' cmd.run "rpm -e joe"`
4. 若要確認是否有成功卸載套件，指令承第 2 步驟

### user
* 在 minion 端增加 / 刪除 / 更改使用者
1. 假設今天要在 minion 端新增一個名為 `test` 的使用者，指令為 `salt 'client' user.add "name=test"`
2. 若想要確認使用者是否被成功新增，可以輸入 `salt 'client' user.getent | grep test`、若需要看到詳細資料（gid、groups、uid 等）需輸入 `salt 'client' user.getent`
3. 若要刪除使用者可以輸入 `salt 'client' user.delete "name=test"`
4. 若要更改使用者名稱可以輸入 `salt 'client' user.rename test test2`
    > 這裡需要注意舊的 name 跟新的 name 之間不能加引號（""） 
> 註記：user.add 可添加的參數還有 `uid`、`gid`、`groups`、`home`、`shell`、`unique`、`system`、`fullname`、`roomnumber`、`workphone`、`homephone`、`other`、`createhome`、`loginclass`、`root`、`nologinit`


### group

## 補充功能

## 延伸學習
1. [SALTSTACK / EXECUTION MODULES](https://docs.saltstack.com/en/latest/ref/modules/all/index.html)