# Mass-balance solver

## **程式目的**

此程式主要目的為幫助正在學習化工系質能均衡課程的學生能夠快速算出複雜的質量守恆問題的計算機。

*此程式只能應用於質量守恆問題，尚不行解決能量和化學反應的相關問題。*




## 程式使用說明
此計算機需要提供您問題當中的相關參數例如:
#### 1.Stream direction *in or out*
#### 2.Chemical component
#### 3.Fraction prefix
#### 4.Process relations
#### 5.Known parameters

### 如何將相關參數放入程式
```python
###### inputs

stream_direction = ["in","in","out","out"] # directions of streams
component_labels = ["Flow rate","Methene","Ethene","Propyne","Nitrogen","Oxygen","CO2"] #chemical components
fraction_prefix = "y" # mass (x) or mole (y) fraction

###### process relation

process_equation = ["F1=0.25*F2","F3=0.5*F4","y2N=0.1*y3P"] ## eg:"F1=0.25*F2"
```
