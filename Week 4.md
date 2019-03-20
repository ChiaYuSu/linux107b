# 第四週筆記
## 為何要學習通配符以及正則表達式
* 面對一堆文本需要分析
* 面對一堆數據需要處理
* 面對冗長的伺服器日誌找錯誤
    + 例: `sed -n '/Error/p' fresh.log | awk '{print $1}'`
## 通配符與正則表達式的差異
* 通配符: 用來匹配符合條件的文件名
* 正則表達式: 用來匹配符合條件的檔案內容
## Linux 三劍客
* sed (stream editor): 行編輯器
* awk: 文本處理工具
* grep (global search regular expression(RE) and print out the line): 查找
## 從基礎至應用（三個學習階段）
1. 正則表達式（sed 及 awk 學習的基礎）
2. sed 基本處理命令
3. awk 更為複雜的文本處裡
## 正則表達式應用的場景
* 查找所有包含 `linux` 的行
* 取出以 `abc` 開頭的所有單詞
* 匹配兩位數、密碼、qq號、身分證字號等
## 正則表達式
1. 單個字符表示
    * 特定字符: 某個具體的字符 `1`、`a`
        * 指令: `grep '1' passwd`
        * 執行結果: 
         ![github](https://raw.githubusercontent.com/ChiaYuSu/linux107b/master/Week%204%20-%20Screenshot/grep%20'1'%20passwd.PNG?token=AZMvABCdS26rG3sn-hgGvGeqzTG4pWh9ks5ckokowA%3D%3D)
    * 範圍內字符: 數字字符 `[0-9]`、小寫字符 `[a-z]`、大寫字符 `[A-Z]`、符號字符 `[,:_/]`、反向字符 `[^]`
        * 指令: `grep '[0-9]' passwd`
        * 執行結果: 
         ![github](https://raw.githubusercontent.com/ChiaYuSu/linux107b/master/Week%204%20-%20Screenshot/grep%20'%5B0-9%5D'%20passwd.PNG?token=AZMvAPqlzbEZeTYJ5qf06DIPIWt41rkRks5ckop_wA%3D%3D)
        * 指令: `grep '[a-z]' passwd`
        * 執行結果: 
         ![github](https://raw.githubusercontent.com/ChiaYuSu/linux107b/master/Week%204%20-%20Screenshot/grep%20'%5Ba-z%5D'%20passwd.PNG?token=AZMvAJ6vIS1YaMQpwyVJoX8Zt5rRdVNJks5ckormwA%3D%3D)
        * 指令: `grep '[a-zA-Z]' passwd`
        * 執行結果: 
         ![github](https://raw.githubusercontent.com/ChiaYuSu/linux107b/master/Week%204%20-%20Screenshot/grep%20'%5Ba-zA-Z%5D'%20passwd.PNG?token=AZMvALdIkVviBTziGxWHYGtt_zlnDAW1ks5ckor1wA%3D%3D)
        * 指令: `grep '[,:_/]' passwd`
        * 執行結果: 
         ![github](https://raw.githubusercontent.com/ChiaYuSu/linux107b/master/Week%204%20-%20Screenshot/grep%20'%5B%E7%AC%A6%E8%99%9F%5D'%20passwd.PNG?token=AZMvANAJNVouc3la-4e0NuBKd9GlXypuks5ckosEwA%3D%3D)
        * 指令: `grep '[^0-9]' passwd`
        * 執行結果: 
         ![github](https://raw.githubusercontent.com/ChiaYuSu/linux107b/master/Week%204%20-%20Screenshot/grep%20'%5B%5E0-9%5D'%20passwd.PNG?token=AZMvAKHs9gZ74XHmIXuA_L507zQhrPFYks5ckoscwA%3D%3D)
        > 注意: 反向字符 `^` 一定要在中括號 `[]` 內
    * 任意字符: 任何一個字符 `'.'`
        * 指令: `grep '.' passwd`
        > 注意: `'[.]'` 和 `\.` 的區別
2. 字符串表示
3. 表達式

