# 第四週筆記
## 為何要學習通配符以及正則表達式
* 面對一堆文本需要分析
* 面對一堆數據需要處理
* 面對冗長的伺服器日誌找錯誤
    + ex：`sed -n '/Error/p' fresh.log | awk '{print $1}'`
## 通配符與正則表達式的差異
* 通配符：用來匹配符合條件的文件名
* 正則表達式：用來匹配符合條件的檔案內容
## Linux 三劍客
* sed (stream editor)：行編輯器
* awk：文本處理工具
* grep (global search regular expression(RE) and print out the line)：查找
> 三者中最大的特點：一行指令，輕鬆搞定
## 從基礎至應用（三個學習階段）
1. 正則表達式（sed 及 awk 學習的基礎）
2. sed 基本處理命令
3. awk 更為複雜的文本處裡
## 正則表達式應用的場景
* 查找所有包含`linux`的行