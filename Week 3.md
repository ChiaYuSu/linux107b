# 第三週筆記
## 通配符 vs. 正規表達式
* 通配符：基本上是給系統命令使用，一般用來匹配文件名或者什麽的用在系統命令中
* 正則表達式：基本上是操作字符串，以行尾單位來匹配字符串使用的

## 通配符意義
* 通配符通常是 Shell 做 Pathname Expansion 時用到的。說白了一般只用於文件名匹配，它是由 Shell 解析的，舉例來說像是 `find`、`ls`、`cp`、`mv`等指令

## 常見的通配符
| 通配符 | 匹配 | 指令 | 結果 |
| ----- | ----- | ----- | ---- |
| * | 0或多個字符 | `a*b` | `aabcb`、`axyzb`、`ab`|
| ? | 任意一個字符 | `a?b` | `abb`、`acb`、`a0b` |
| [list] | list 中任意單一字符 | `a[xyz]b` | `axb`、`ayb`、`azb` |
| [!list]<br>[^list] | 除 list 中的任意單一字符 | `a[!0-9]` | `axb`、`ayb`、`azb` |
| [c1-c2] | c1-c2 中的任意單一字符 | `a[0-9]b` | `a0b`、`a1b`、`a2b` |
| [!c1-c2]<br>[^c1-c2] | 不在 c1-c2 中的任意字符 | `a[!0-9]b` | `acb`、`adb` |
| {string1,string2,...} | string1 或 string2 其一字符串 | `a{abc,xyz,123}b` | `aabcb`、`axyzb`、`a123b` |

## 正則表達式常見特殊字符
| 字符 | 含意 |
| ---- | ---- |
| `^` | 指向一行的開頭 |
| `$` | 指向一行的結尾 |
| `.` | 任意單個字符 |
| `[]` | 字符範圍 |
> 如果想將上述字符做為普通字符，就需要在他們前面加上 `\` 字符。舉例來說，如果又使用 `$` 字符，需要在 `$` 前面加上跳脫字元符號 (`\`) ，寫成 `\$`。

## 正則表達式特殊匹配模式
| 匹配模式 | 匹配內容 |
| ---- | ---- |
| [:alnum:] | 字母與數字 |
| [:alpha:] | 字母 |
| [:ascii:] | ASCII 字符 |
| [:blank:] | 空格 |
| [:digit:] | 數字 |
| [:lower:] | 小寫字母 |
| [:upper:] | 大寫字母 |
> 註：若要查找檔案內所有的數字，可輸入 `grep [[:digit:]] passwd` (`passwd` 為用戶的認證訊息資料)

## DNS Server（名稱解析伺服器）
* UDP、53 port，分為 client 端 與 server 端
    * UDP 為非連線導向式的傳輸協定
* 攻擊方法
    1. DNS Spoofing：傳輸過程中封包被竄改，導向錯誤的 ip / Domain Name
    2. DDoS：駭客將 DNS Server 的 Destination 導向為 victim（受害者）
* 有兩種方向
    1. 正向解析：Domain Name -> ip
    2. 反向解析：ip -> Domain Name
* 常見的 DNS Server
    * 1.1.1.1：[CloudFlare](https://zh.wikipedia.org/zh-tw/CloudFlare)
    * 4.4.4.4：Google
    * 8.8.8.8：Google
    * 9.9.9.9：[IBM（Quad9）](https://en.wikipedia.org/wiki/Quad9)
    * 180.76.76.76：Baidu
* 負載均衡作用（Load Balancer）
* 阻擋國外 ip，不提供相對應服務
* 可參考延伸學習第 3 點

## 內部 DNS 與外部 DNS 差異
* 透過校內的 DNS `10.10.10.3` 會回傳 `10.10.10.20` 的 ip，而透過 Google 的 DNS `8.8.8.8` 會回傳 `203.72.226.40` 的 ip
    * 會有這樣的差異是因為前者使用該網域的權限管轄伺服器，後者是使用外部公開的 DNS，因此產生不同的結果
> 備註：前者的 ip `10.10.10.20` 只有在**內部網路**才能連線上

## 延伸學習
1. [51CTO博客 / linux通配符和正则表达式](https://blog.51cto.com/qibingtuan/1970593)
2. [CSDN 小小攻城师 / 通配符和正则表达式的区别](https://blog.csdn.net/huiguixian/article/details/6284834)
3. [慕課網 imooc Jeson 老師 / Linux 智能DNS](https://www.imooc.com/learn/768)

## 本週作業
