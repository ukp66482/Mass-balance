# Mass-balance solver (python)

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
![291963577_333417485666349_5528480416349607031_n](https://user-images.githubusercontent.com/109071424/178447152-8c627296-7d4d-4ab3-97e3-162e80fdbf7d.jpg)

我們必須照著上圖的方式將您題目中的已知項命名，加入在input fraction , flow rate的區域
```python
###### input fraction , flow rate
y1N = 0.7808
F2 = 100
y2O = 0.9
y1O = 0.2098
y2N = 0.01
y3A = 0
######
```
### 執行程式
當您照著上述方式將所有參數輸入並執行程式你就可以得到自動產生的flow table
![final](https://user-images.githubusercontent.com/109071424/178450695-1514a731-5f88-4d27-8a31-de8e3a44cc87.png)

這樣就能夠讓您在學習質量均衡的過程中，減少很多不必要的運算，將複雜的工作交給電腦處理。

## 程式內容簡介

### 初始化Flow table的生成
在這個程式當中，我們透過引用python中的pandas來創建在質能均衡課中我們常用的flow table
```python
stream_labels = []
for i in range(Ns):
    stream_labels.append("stream " + str(i+1) + "-" + stream_direction[i])


V_empty = np.empty([Ns, Nc+1],dtype=object)  


df = pandas.DataFrame(V_empty, index=stream_labels, columns=component_labels)
print (df)
```
下圖為新生成的Flow table

![1](https://user-images.githubusercontent.com/109071424/178454275-157b072e-3519-4887-8c11-43f864fde402.png)

### 將已知放入Flow table當中
在這邊我們主要的動作是將未知數用python sympy中的symbol來生成並判斷是否已經宣告他數值，如果已經宣告那就將數值放入flow table，反之則用symbol來做取代。
```python
all_variables = []


for i in range(len(stream_direction)):
    all_variables.append(component_labels[0][0] + str(i+1))


for i in range(len(component_labels[1:])): 
    for j in range(len(stream_direction)):
        all_variables.append(fraction_prefix + str(j+1) 
                             + component_labels[i+1][0])
        


all_symbols = sympy.symbols(all_variables)




V_initial = V_empty.copy()

given_variables = []

unknown_index=[]
for i in range(len(all_variables)):
    
 
    index_row = int(all_variables[i][1]) - 1
    
    if all_variables[i][0] == component_labels[0][0]:
        index_column = 0
    
    else:
        for j in range(len(component_labels)):
            
            if all_variables[i][2] == component_labels[j][0]:
                index_column = j
    
    if all_variables[i] in globals():

        V_initial[index_row, index_column] = eval(all_variables[i])
        given_variables.append(all_variables[i])
    else:
        V_initial[index_row, index_column] = all_symbols[i]
        unknown_index.append([index_row,index_column])


print("\n")

 
df = pandas.DataFrame(V_initial, index=stream_labels, columns=component_labels)
print (df)   
```
### 變數分析及告知
    此程式最重要的地方就是這部分，這邊我們沒有使用在課程中所學的自由度(degree of freedom)來做分析，而是透過designed variables的方式。
此區域透過程式讓我們可以知道所有variables的數量且可找出我們有幾個designed variables，並判斷是否提供太多未知數或提供太少未知數。

在質能均衡課程當中我們知道如果題目給了足夠的未知數但沒有跟flow rate相關我們也沒辦法求出正確的答案，此程式也能判斷出這個情況。
```python
Ns = len(stream_direction)
Nc = len(component_labels) - 1 
Np = len(process_equation)
-----------------------------------------------------------------------------------------------------------------------------------------------------------------------
N_zeros = np.count_nonzero(V_initial == 0) 
Nv = Ns*(Nc+1) - N_zeros + Np            
Nd = Nv - Ns - Nc -Np                   


print ("\nThe number of total variables (zeros not included):", Nv) 
print (colored("The number of design variables:",attrs=["bold"]), Nd)
print ("\nYou have given", len(given_variables)-N_zeros+Np, "variables")

if len(given_variables)-N_zeros+Np > Nd:
    print (colored("You have given too many input variables","red"))
if len(given_variables)-N_zeros+Np < Nd:
    print (colored("You didn't give enough input variables","red"))
    
    
u=0
for i in range(len(stream_direction)):
    if all_variables[i] not in globals():
        u+=1
    if u==len(stream_direction):
        print(colored("\nYou didn't provide any Flow information so can't solve the problem","red"))
        
unknown_variables = [element for element in all_variables if element not in given_variables]
number_unknowns = len(unknown_variables)
print ("\nThere are", number_unknowns, "unknown variables:")
print (*unknown_variables,sep=" ,")
```
![各類情況](https://user-images.githubusercontent.com/109071424/178460295-403b5ea0-1b27-4365-9cc6-63a95557580e.png)
上圖為各類可能出現的情況

### 求得解的判定

就算問題是可以的情況，我們也會遇到許多問題。

```python
solutions = sympy.solve(sym_equations,unknown_variables)

solutions = eval(str(solutions))


Final_V = V_initial.copy()

if len(solutions)!=0:

    print("\nThe solutions are:")

    for i in range(len(unknown_variables)):
        print(unknown_variables[i],"=",sympy.Float(solutions[0][i],5))
    
    for i in range(len(unknown_variables)):
        Final_V[unknown_index[i][0],unknown_index[i][1]] =sympy.Float(solutions[0][i],5)

    for i in range(len(solutions[0])):
        if int(solutions[0][i])<0:
            print(colored("\nWarning !! The solution contains negative numbers","red"))
            break

elif len(solutions)==0 and len(given_variables)-N_zeros+Np == Nd:
    
    print(colored("\nWarning there should be existing dependent equations","red",attrs=["bold"]))

print ("\n")    
```

1.求得的solution為解析解

&ensp;當發生這個情況我們必須講已知帶入solution當中，就可以得到正常的答案。

2.如果solution有負數

&ensp;在質能均衡的問題當中通常是不會出現負數的，因為解就是濃度和流量，所以不該有負數解。
![nega](https://user-images.githubusercontent.com/109071424/178462194-b0701b54-b51c-42dd-9252-14a0a7e011ae.png)

3.出現dependent equations

&ensp;當提供的已知數洽巧讓方程式產生dependent equations將會讓solution為一個空集合，程式也會提示出可能存在dependent equations

![de](https://user-images.githubusercontent.com/109071424/178463676-3209480c-9295-4906-8221-ed6e549aaade.png)

## 此程式未來展望

1.GUI介面

此程式目前沒有GUI，因本人也是第一次接觸python，希望在將來能夠加入GUI介面讓用更加方便。

2.將化學反應和能量守恆放入其中

質能均衡在加入化學反應後，將會讓變數分析需要全部重寫難度甚高，希望將來夠將其加入。
