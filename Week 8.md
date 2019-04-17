# 第八週（期中考週）
## 準備三台虛擬伺服器
* 目的：附載均衡（Load Balancer）
* 相關硬體軟體：Haproxy、Nginx

## LVS（Linux Virtual Server）工作模式
* NAT
* Tunnel
* DR

## LVS（Linux Virtual Server）
* 是基於 Layer 4 （連線層）的附載均衡，不支持複雜特性的附載均衡，Layer 4 較簡單（可以看到排程）
* 效率較 Layer 7（應用層）來得高，Layer 7 較困難

## NAT
* 安裝三台虛擬機
* 查詢三台 ip 位址



## 常見的排程演算法
* RR（Round Robin）
* WRR（Weight Round Robin）
* 最少連線數優先
* SRC-IP Hash（雜湊函數）

## 延伸學習
1. [麥斯奇科技 maxkit/三種 LVS 的模式：LVS-NAT、LVS-TUN、LVS-DR](http://blog.maxkit.com.tw/2016/05/lvs-lvs-natlvs-tunlvs-dr.html)
2. [51CTO博客/Lvs之NAT、DR、TUN三种模式的应用配置案例](https://blog.51cto.com/lansgg/1229421)
3. [The Will Will Web/使用 ApacheBench 進行網站的壓力測試](https://blog.miniasp.com/post/2008/06/30/Using-ApacheBench-ab-to-to-Web-stress-test)