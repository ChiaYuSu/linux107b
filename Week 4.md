# 第四週筆記
## 複習上週講的通配符
* 可參考延伸學習第 1 點

## 反向解析伺服器
* `dig -x [ip] @DNS Server`：建置成功後，輸入此指令測試是否成功

## Docker 安裝
1. CentOS 7 有內建 Docker 套件，以 yum 指令透過網路安裝最新套件 `yum -y install docker`
2. 啟動 docker 服務 `systemctl start docker`
3. 設定每次開機後自動啟動 `systemctl enable docker`
4. 下載 CentOS 7 的 Image `docker pull centos:7`
5. 輸入 `docker images`，檢查是否成功下載

## 啟動、離開及終止 docker
1. 啟動 docker：`docker run -it centos:7 /bin/bash` 
2. 離開 docker：按住鍵盤上 Control 鍵 + P + Q (狀態：未關閉 docker)
    * 若要回到未關閉的 docker：`docker attach Container_ID`
3. 終止 docker：`exit`

## Docker 刪除 Images 及刪除 Container
1. 刪除 Images
    * 一個一個 Container 刪除：`docker rmi Image_Repository_tag`
    * 強制刪除所有 Images：`docker rmi -f $(docker images -a -q)`
2. 刪除 Container
    * 一個一個 Container 刪除：`docker rm -f Container_ID`
    * 強制刪除所有 Container：`docker rm -f $(docker ps -a -q)`

## 將 docker 重新命名並 push 至 Docker 官網
1. `docker tag server:tag new_name/new_server:new_tag`，前面的 `server:tag` 輸入要重命名的 server 名稱及版本 (tag)，後面的則輸入自訂資訊，舉例來說我輸入的是 `docker tag centos:7 jack879tw/test:0.1`
2. 重命名後輸入 `docker images` 確認是否成功
3. 成功後輸入 `docker login`，登入自己 docker 的帳號及密碼
4. 最後將重命名的 docker push 至 Docker 官網，指令為 `docker push new_name/new_server:new_tag`

## 延伸學習
1. [51CTO 博客 / linux通配符和正则表达式](https://blog.51cto.com/qibingtuan/1970593)
2. [Docker官網 / Docker: Enterprise Application Container Platform](https://www.docker.com/)

