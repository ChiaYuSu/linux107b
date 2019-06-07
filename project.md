# 期末報告
## 主題
* Saltstack

## Saltstack 是什麼

## Saltstack vs. Ansible vs. Puppet vs. Chef

## 環境架設

## 功能
### copy
* 將檔案從 master 拷貝到 minion
1. 首先，在 master 的 `/tmp` 目錄下建立一個 `test.txt` 的檔案，指令為 `cd /tmp` -> `echo "hello world" > test.txt`
2. 將 master `test.txt` 拷貝至 minion 的 `/tmp` 目錄下，指令為 `salt-cp 'client' /tmp/test.txt /tmp`
3. 完成後確認 minion 端是否真的有此檔案，指令為 `salt 'client' cmd.run "cat /tmp/test.txt"`


### remove
### fetch
### yum
### user
### group