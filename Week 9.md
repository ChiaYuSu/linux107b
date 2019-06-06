# 第九週筆記（期中考週）
## 準備三台虛擬伺服器
* 目的：附載均衡（Load Balancer），把用戶端的請求適當分配給不同的伺服器
* 相關硬體軟體：Haproxy、Nginx、FS

## 專有名詞介紹
* CIP：Client IP
* VIP：Virtual IP
* RIP：Real IP
* RS：Real Server

## LVS（Linux Virtual Server）三種工作模式
* NAT
* IP Tunneling
* DR（Direct Routing）

## LVS（Linux Virtual Server）
* 是基於 Layer 4 （連線層）的附載均衡，不支持複雜特性的附載均衡，Layer 4 較簡單（可以看到排程）
* 效率較 Layer 7（應用層）來得高，Layer 7 較困難

## LVS NAT 基本性質

1. Cluster Nodes（Real Server）和 Load Balancer 在同一網段
2. Real Server 的 RIP 地址一般為 Private IP
3. Load Balancer 負責處理 Client 和 Real Server 之間的所有進出的封包
4. Real Server 將 Load Balancer 的內網 IP 作為 Default Gateway
5. 只需要在 Load Balancer 上配置一個 Public IP 就可以了
6. Load Balancer 支援 Port Mapping
7. Real Server 可以使用任何一種 OS
8. 因為 Load Balancer 負責所有資料的進出，所以在大型應用場景中，Load Balancer 容易成為系統瓶頸。此外，因為 NAT 的關係，Load Balancer 必須將 Reqeust 和 Response 進行 IP 改寫，因此網站訪問量比較大的時候 Load Balancer 會有比較大的瓶頸，一般要求最多只能 10-20 台節點

## LVS IP TUNNELING 基本性質
1. Cluster Nodes 可跨越 Internet（處於不同的物理網路中）
2. Real Server 的 Real IP 必須是 Public IP
3. Load Balancer 只需要處理 Reqeust，Response 由 Real Server 直接發送給 Client
4. 只有支援 Tunnel 功能的 OS 才能用於 Real Server
5. Load Balancer 不支援 Port Mapping
6. Real Server 的 default gateway 不能指向 Load Balancer
7. Real Server 主機需要綁定 VIP 地址在 LO 接口上（各個服務器有相同的 VIP 和單獨的 RIP）

## LVS DR 基本性質
1. Load Balancer 與 Real Server 必須在同一個物理網路中；
2. Real Server 的 RIP 地址不必是私有地址，如可為 Public IP 可以實現遠端管理
3. Load Balancer 只負責處理 Request，Response 由 Real Server 直接發往 Client
4. Real Server 不能將 Gateway 指向 Load Balancer，而是直接配置為上層路由的 Gateway
5. Load Balancer 不支援 port mapping
6. 大多數 OS 都可以用在 Real Server
7. Real Server 主機需要綁定 VIP 地址在 LO 接口上（有相同的 VIP 和單獨的 RIP），並且需要設定 ARP 抑制（由於網絡接口都會進行ARP廣播回應，但 Cluster 的其他機器都有這個 VIP 的 LO 接口，都回應就會衝突。所以我們需要把 Real Server 的 LO 接口的 ARP 回應關閉掉）

## LVS 三種模式比較表
| 選項 | NAT | TUN | DR |
| --- | --- | --- | --- |
| Server OS | 任意 | OS 需要支援 IP Tunnel（目前僅有 Linux 支援）| 多數（支援 Non-ARP）|
| 服務器網路 | LAN | LAN/WAN | LAN |
| Real Server 數目（100M 網路） | 10-20 | 100 | 100 |
| Server 的網路 Gateway | Load Balancer | 自己的路由 | 自己的路由 |
| 效率 | 一般 | 高 | 最高 |
| 擴展性 | 差，所有 traffic 都要經過 Load Balancer | 好，但需要建立 IP Tunnel，適合用在跨境 Data Center 的環境 | 好，但要求 Load Balancer 跟 Real Server 在同一個 LAN 裡面 |
| Server 安全性 | 好，Real Server 使用內部 IP，隱密 | 差，Real Server 使用 Public IP | 差，Real Server 使用 Public IP |
| Public IP 需求 | 需要一個 IP 作為 Server VIP（Virtual IP）| 除了 VIP 之外，每個 Server 都需要有合法的 IP 可直接連接到客戶端 | 除了 VIP 之外，每個 Server 都需要有合法的 IP 可直接連接到客戶端 |

## 常見的排程（Scheduling）演算法
* RR（Round Robin）
* WRR（Weight Round Robin）
* Least Session（最少連線數優先）
* SRC-IP Hash（雜湊函數）

## 報告
* 已經在金大學習三學期了，老師希望大家期末之前能夠上台報告，關於伺服器或是腳本程式

## 延伸學習
1. [麥斯奇科技 maxkit/三種 LVS 的模式：LVS-NAT、LVS-TUN、LVS-DR](http://blog.maxkit.com.tw/2016/05/lvs-lvs-natlvs-tunlvs-dr.html)
2. [51CTO博客/Lvs之NAT、DR、TUN三种模式的应用配置案例](https://blog.51cto.com/lansgg/1229421)
3. [The Will Will Web/使用 ApacheBench 進行網站的壓力測試](https://blog.miniasp.com/post/2008/06/30/Using-ApacheBench-ab-to-to-Web-stress-test)
4. [碼人日誌/Siege – HTTP 網站壓力測試好幫手](https://coder.tw/?p=7198)