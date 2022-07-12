# Mass-balance solver

## **程式目的**

此程式主要目的為幫助正在學習化工系質能均衡課程的學生能夠快速算出複雜的質量守恆問題的計算機，且在最後能夠生成我們熟悉的Flow table讓我們對數據能夠更好解讀，並且此計算機具備變數分析的能力，只要提供的未知數不夠或過多都會給予警示。

*此程式只能應用於質量守恆問題，尚不行解決能量和化學反應的相關問題。*



## 程式使用說明
此計算機需要提供您問題當中的相關參數例如:
####  1.Stream direction *in or out*
####  2.Chemical component
####  3.Fraction prefix
####  4.Process relations
####  5.Known parameters

### 如何將相關參數放入程式
我們直接透過一個簡單的例題來做輸入的教學:![example](https://user-images.githubusercontent.com/109071424/178442376-9d614242-86b5-4436-9b96-0f9793d24aca.png)

1.從題目當中我們可以知道有 Stream 1 , Stream 2 , Stream 3 三個stream，方向分別為 in , out , out 所以我們打開程式碼在inputs的stream directions 部分放入
```python
stream_direction = ["in","in","out"] # directions of streams
```
2.再來我們需要讓程式知道我們有什麼化學物質，透過題目我們知道有 Oxygen , Argon , Nitrogen，所以我們將程式中component_labels的部分在flow rate後方將其加入
```python
component_labels = ["Flow rate", "Oxygen", "Nitrogen", "Argon"]
```
3.fraction prefix 我們通常將莫耳濃度設為y 重量百分濃度設為x，這只是我本人的習慣，大家可以隨意設定不影響程式運行。
```python
fraction_prefix = "y" # mass (x) or mole (y) fraction
```
4.再來是process relations 此題沒有相關假設，所以直接空白即可
```python
###### process relation

process_equation = [] ## eg:"F1=0.25*F2"
```
5.最後我們將題目給的所有已知放入，因前面我們已經將fraction prefix設為y，所以在提供已知項時就要符合您的假設，以上圖右側y1O來舉例
