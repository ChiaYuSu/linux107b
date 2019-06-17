# 第二週筆記
## 遠端桌面（RDP、VNC）
* 建議使用 putty、secureCRT、XShell 連線到客機
* Windows 上能透過 `netstat -an` 查看有哪些 Port 被佔用
    * `netstat -an | findstr 3389` 能過濾 3389 號 Port
    * `findstr` 類似於 Linux 中的 `grep`
    * 3306 Port 用於 MySQL
    * 3389 Port 用於遠端桌面（VNC）
* RDP：Windows 有內建 Mstsc 連線軟體
    * 遠端桌面除了遠端操作外，也可以用於教學用途
    * mstsc 只有支援 RDP 協定，並不支援 VNC 協定，需另外下載第三方的軟體
* `0.0.0.0` 代表意義
    * 機器未開機好（尚未申請伺服器），一開始的位址
    * 機器任一介面的位址（之後 Saltstack 配置檔會設定到，代表意義在於 master 端能夠接收所有 minion 端的 ip）
    * 如果路由表其他的規則都找不到，會去使用 `0.0.0.0` 的路由
* Server 端通常會綁定一組特定 ip，如果在本機上 127.0.0.1 能連線，但在其他電腦上這個 ip 就連不上

## Linux 三劍客
* 三劍客為 grep、awk、sed

### grep 常用指令
* `grep -i`：不分大小寫
* `grep -v`：排除特定字元
* `grep -o`：只顯示特定字串

### awk 常用指令
* `awk '{print $2}'`：取出第二欄的 ip

### sed 常用指令
* 通常會搭配正規表達式，待會會介紹

## 正規表達式
* `.`：代表任一字元（又稱「萬用字元」）
* `*`：代表任意次數
* `^`：代表以 ... 為開頭
* `$`：代表以 ... 為結尾

## Google 二次認證（Google Authenticator）
* 可參考延伸學習第 1 點
* 要記得先關閉 selinux 跟防火牆
    * selinux 關閉方式：`vi /etc/sysconfig/selinux` -> 找到 `SELINUX=enforcing` 將其改成 `SELINUX=disabled` -> 重機啟動虛擬機 `reboot`
    * 防火牆關閉方式：`systemctl stop firewalld`


## 延伸學習
1. [Ken Wu / CentOS 7 SSH 兩步驟驗證 (Using Google Authenticator)](https://kenwu0310.wordpress.com/2016/12/09/centos-7-ssh-%E9%9B%99%E5%9B%A0%E7%B4%A0%E8%AA%8D%E8%AD%89-using-google-authenticator/)
2. [Linux 技術手札 / CentOS 7 關閉防火牆及 SELinux](https://www.opencli.com/linux/centos-7-disable-firewalld-selinux)