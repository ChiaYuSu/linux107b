# 第十二週筆記
## 上週回顧
* 直接監控 vs 間接監控
    * 直接監控：可透果主機是否 alive 或 server 是否有成功執行判斷（例如透過 ping 來測試主機是否上線中）
    * 間接監控：週期性（固定一段時間）去抓取，能掌握 CPU、硬碟空間、記憶體等較詳細的資訊、如 NRPE (Nagios Remote Plugin Executor)

## 間接監控（延續上週進度）
### 被控端
* `vim /etc/nrpe.d/lcgdm-common.cfg`：先確認參數是否對應到相對應的 plugins 路徑
    * 至少需要以下兩行
        ```
        command[check_cpu]=/usr/lib64/nagios/plugins/lcgdm/check_cpu
        command[check_network]=/usr/lib64/nagios/plugins/lcgdm/check_network
        ```
* 在被控端上測試 plugins 能否執行
```
/usr/lib64/nagios/plugins/lcgdm/check_cpu 
/usr/lib64/nagios/plugins/lcgdm/check_network 
systemctl start nrpe
```

### 主控端
* 主控端上測試，能否透過 nrpe 執行擷取被控端上執行後的訊息
```
cd /usr/lib64/nagios/plugins 
./check_nrpe -H 192.168.56.104 -c check_cpu
./check_nrpe -H 192.168.56.104 -c check_network
```
* 新增指令：`gedit /etc/nagios/objects/commands.cfg`
```
define command {
    command_name    check_nrpe
    command_line    $USER1$/check_nrpe -H $HOSTADDRESS$ -c $ARG1$
}
```
* 新增服務：`gedit /etc/nagios/objects/localhost.cfg`
```
define service {
    use                     local-service
    host_name               web1
    service_description     check cpu
    check_command           check_nrpe!check_cpu
    notifications_enabled   0
}

define service {
    use                     local-service
    host_name               web1
    service_description     check networks
    check_command           check_nrpe!check_network
    notifications_enabled   0
}
```
* 重啟 nagios：`systemctl restart nagios`
* 到 web 端查看 host:web1 是否新增 cpu 及 network 的監控

## 手動設定監控內容
* 有時我們想要監控的資訊可能監控軟體沒有提供，因此需要靠自己手動達成
* 在被控端輸入 `gedit /usr/lib64/nagios/plugins/check_waiting_connect`，將腳本寫入
    * 腳本如下
        ```
        # !/bin/bash
        STATE_OK=0
        STATE_CRITICAL=2
        W=`netstat -an | wc -l` # 統計有幾行（等同於有多少機器連線此電腦），並將值賦予給 W
        if [ $W -le 1000 ]; then
            echo "OK, waiting connections < 1000 low"
            exit $STATE_OK;
        else
            echo "WARNING, waiting connections > 1000 high"
            exit $STATE_CRITICAL
        fi
        ```
* 被控端新增參數：`vim /etc/nrpe.d/lcgdm-common.cfg`
    * 需加入此行：`command[check_waiting_connect]=/usr/lib64/nagios/plugins/check_waiting_connect`
* 被控端測試（測試前記得先設定執行權限）：`chmod +x /usr/lib64/nagios/plugins/check_waiting_connect` -> `/usr/lib64/nagios/plugins/check_waiting_connect`
* 主控端測試：`cd /usr/lib64/nagios/plugins` -> `./check_nrpe -H 192.168.56.104 -c check_waiting_connect`
* 新增服務：`gedit /etc/nagios/objects/localhost.cfg`
```
define service {
    use                     local-service
    host_name               web1
    service_description     check waiting connect
    check_command           check_nrpe!check_waiting_connect
    notifications_enabled   0
}
```
* 重啟 nagios：`systemctl restart nagios`
* 到 web 端查看 host:web1 是否新增 check_waiting_connect 的監控

## nagios 狀態
* 一共有 4 種狀態
    * 0：OK（綠色）
    * 1：WARNING（黃色）
    * 2：CRITAL（紅色）
    * 3：UNKONWN（橘色）

## 除錯
* 若在主監控端出現 5666，代表被監控端代理沒有打開，需在被監控端輸入 `systemctl start nrpe`，打開 NRPE

## NSClient++
* 在 Windows 上安裝 NSClient++
* Configuration 全打勾，Allowed Host 輸入主控端 ip
* 在監控端中編輯 `/etc/nagios/nagios.cfg`，取消註解，開放讓 Windows 使用，然後再將 define host 的 address 改成 `192.168.56.1`
    * 將 `# cfg_file=/etc/nagios/objects/windows.cfg` 改成 `cfg_file=/etc/nagios/objects/windows.cfg`

## 下週上課內容
* Ansible
    * 相關軟體還有 Saltstack、Puppet

## 期末大作業
* 伺服器的簡介（最晚第 16 週開始報告），老師上課沒有講到的主題都可以做，或是寫一個腳本來介紹


## 延伸學習
1. [ITREAD/Nagios利用NSClient++監控Windows主機](https://www.itread01.com/content/1505803218.html)