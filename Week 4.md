# 第四週筆記
## 為何要學習通配符以及正則表達式
* 面對一堆文本需要分析
* 面對一堆數據需要處理
* 面對冗長的伺服器日誌找錯誤
    + 例：`sed -n '/Error/p' fresh.log | awk '{print $1}'`

## 通配符與正則表達式
* 通配符和正則表達式看起來有點像，要注意部要混淆了
    * 通配符：用來匹配符合條件的文件名，通常只有 `*`、`?`、`[]`、`{}`這四種
    * 正則表達式：用來匹配符合條件的檔案內容

## Linux 三劍客
* sed (stream editor)：行編輯器
* awk：文本處理工具
* grep (global search regular expression(RE) and print out the line)：查找

## 從基礎至應用 (三個學習階段)
1. 正則表達式 (sed 及 awk 學習的基礎)
2. sed 基本處理命令
3. awk 更為複雜的文本處裡

## 正則表達式應用的場景
* 查找所有包含 `linux` 的行
* 取出以 `abc` 開頭的所有單詞
* 匹配兩位數、密碼、qq號、身分證字號等

## 正則表達式
指令格式：`grep 'match_pattern' file_name`
1. 單個字符表示
    * 特定字符：
        * 某個具體的字符 `1`、`a`
    * 範圍字符：
        * 數字字符 `[0-9]`
        * 小寫字符 `[a-z]`
        * 大寫字符 `[A-Z]`
        * 符號字符 `[,:_/]`
        * 反向字符 `[^]` (反向字符 `^` 一定要在中括號 `[]` 內)
    * 任意字符：
        * 任何一個字符 `.` (`[.]`、`\.` 這裡的小數點代表包含小數點的文本 ( `\` 代表跳脫字符)，`.` 代表任何一個字符，兩者代表的意思不同)
    * 其他字符：
        * 頭字符 `^` (`[^]` 代表範圍內的反向字符，`^` 代表頭字符，兩者代表意思不同)
        * 尾字符 `$`
        * 空行 `^$` (`^$` 若該行有按空白鍵 (space) 的話則不會印出，linux 會識別為該行有文本存在)
    * 元字符：
        * 任何字類字符 `\w` (同等於 `[A-Za-z0-9_]`)
        * 任何非字類字符 `\W` (同等於 `[^A-Za-z0-9_]`)
        * 單詞的分割 (`'\bx\b'` 僅搜尋代表密碼的 `x`，不包含非密碼的 `x`)
2. 字符串表示
    * 字符組合 (固定次數)：
        * 代表零次或多次 `*` (同等於 `{0, }` )
        * 代表一次或多次 `+` (同等於 `{1, }` )
        * 代表零次或一次 `?` (同等於 `{0,1}` )
    * 字符組合 (重複特定次數)：
        * 重複特定次數 `{m,n}` (`m` 代表重複次數最小值，`n` 代表重複次數最大值)
3. 表達式

## Docker 安裝
1. CentOS 7 有內建 Docker 套件，以 yum 指令透過網路安裝最新套件 `yum -y install docker`
2. 啟動 docker 服務 `systemctl start docker`
3. 設定每次開機後自動啟動 `systemctl enable docker`
4. 下載 CentOS 7 的 Image `docker pull centos:7`
5. 輸入 `docker images`，檢查是否成功下載

## 啟動、離開及終止 docker
1. 啟動 docker：`docker run -it centos:7 /bin/bash` 
2. 離開 docker：按住鍵盤上 Control 鍵 + P + Q
3. 終止 docker：`exit`

## Docker 刪除 Images 及刪除 Container
1. 刪除 Images
    * 一個一個 Container 刪除：`docker rmi Image_Repository_tag`
    * 強制刪除所有 Images：`docker rmi -f $(docker images -a -q)`
2. 刪除 Container
    * 一個一個 Container 刪除：`docker rm Container_ID`
    * 強制刪除所有 Container：`docker rm -f $(docker ps -a -q`

## 將 docker 重新命名並 push 至 Docker 官網
1. `docker tag server:tag new_name/new_server:new_tag`，前面的 `server:tag` 輸入要重命名的 server 名稱及版本 (tag)，後面的則輸入自訂資訊，舉例來說我輸入的是 `docker tag centos:7 jack879tw/test:0.1`
2. 重命名後輸入 `docker images` 確認是否成功
3. 成功後輸入 `docker login`，登入自己 docker 的帳號及密碼
4. 最後將重命名的 docker push 至 Docker 官網，指令為 `docker push new_name/new_server:new_tag`

## 延伸學習
1. [慕课网 / 实例妙解Sed和Awk的秘密](https://www.imooc.com/learn/819)
2. [鳥哥的 Linux 的私房菜 / 第十一章、正規表示法與文件格式化處理](http://linux.vbird.org/linux_basic/0330regularex.php)
3. [Docker官網 / Docker: Enterprise Application Container Platform](https://www.docker.com/)

