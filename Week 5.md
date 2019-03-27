# 第五週筆記
* `-A` 印出各行尾字符 `$`
    * `cat -n a.txt`
    * `cat -n -A a.txt`

* 通配符 vs. 正則表達式

* `-E` vs. `\+` vs. `egrep`
    * `grep "se\+"`
    * `grep -E "se+"`
    * `egrep "se+"`

* `-o`

* 正則表達式總結
    * 正則很無情
    * 正則很大度
    * 跳脫字元使用 `egrep` 

* `sort`：排序
* `uniq -c`：
* `cd -`：回到上一個所在的目錄
* `cat !$`：顯示上一個指令最後一個參數
* `docker run --rm centos:7`：`--rm` 代表 docker 執行完自動消滅
* `docker run -d -p 8080:80 httpd`：`-d` 代表在背景執行，`-p` 代表將 本地的 8080 埠映射到 docker 的 80 埠
* `docker inspect centos:7`：查看容器的資訊
* `docker -e`：把系統環境變量傳進去給 docker
* `docker --help`：docker 指令查詢 

## 本地端
* `docker run -d -p 8080:80 -v /mydata:/usr/local/apache2/htdocs httpd`

## 期中考、期末考
* 期中考：找一個上課沒講過的伺服器並報告
* 期末考：收筆記

## 延伸學習
1. [慕课网 / 实例妙解Sed和Awk的秘密](https://www.imooc.com/learn/819)