# 第十一週筆記
## 上週回顧
* 直接監控 vs 間接監控
    * 直接監控：可透果主機是否 alive 或 server 是否有成功執行判斷（例如透過 ping 來測試主機是否上線中）
    * 間接監控：週期性（固定一段時間）去抓取，能掌握 CPU、硬碟空間、記憶體等較詳細的資訊、如 NRPE (Nagios Remote Plugin Executor)

## 間接監控
### 主控端
* 

### 被控端
```
yum install epel-release nrpe nagios-plugins*
vim /etc/nagios/nrpe.cfg # 新增 allow-hosts 127.0.0.1, 192.168.56.102 (我的主控端 IP)
systemctl start nrpe
```

## nagios 狀態
* 一共有 4 種狀態
    * 0：OK（綠色）
    * 1：WARNING（黃色）
    * 2：CRITAL（紅色）
    * 3：UNKONWN（橘色）

## 除錯
* 若在主監控端出現 5666，代表被監控端代理沒有打開，需在被監控端輸入 `systemctl start nrpe`，打開 NRPE

## 下週上課內容
* Ansible
    * 相關軟體還有 Saltstack、Puppet

## 延伸學習
1. [ITREAD/Nagios利用NSClient++監控Windows主機](https://www.itread01.com/content/1505803218.html)