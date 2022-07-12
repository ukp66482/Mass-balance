# Mass-balance solver

## **程式目的**

此程式主要目的為幫助正在學習化工系質能均衡課程的學生能夠快速算出複雜的質量守恆問題的計算機，並且此計算機具備變數分析的能力，只要提供的未知數不夠或過多都會給予警示。

*此程式只能應用於質量守恆問題，尚不行解決能量和化學反應的相關問題。*



## 程式使用說明
此計算機需要提供您問題當中的相關參數例如:
####  1.Stream direction *in or out*
####  2.Chemical component
####  3.Fraction prefix
####  4.Process relations
####  5.Known parameters

### 如何將相關參數放入程式
我們直接透過一個簡單的例題來做輸入的教學![example](https://user-images.githubusercontent.com/109071424/178442376-9d614242-86b5-4436-9b96-0f9793d24aca.png)



打開程式碼馬上可以看到一區叫做 inputs 和 process relations的部分，我們需要將所有知道的參數在此區域進行輸入:
