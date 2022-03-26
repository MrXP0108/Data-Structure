# 陣列

## 靜態陣列
- 靜態陣列的名稱為一唯讀的指標（或作指標常數）。
- 承上一句的敘述，靜態陣列作為參數傳遞時不需要也不必要使用到傳址運算子 `&`。
- `*month` 即為 `month[0]`，因此 `*(month + 5)` 跟 `month[5]` 是一樣的東西。
- 靜態陣列的容量固定。若想要將其容量非固定化，我們需要使用動態陣列。

## 動態陣列
- 創建動態陣列的實作（以 `int` 為例）：
  ```c
  #include <stdlib.h> // 用 malloc.h 也可以

  int* dynamicIntArr = (int*)malloc(arr_size * sizeof(int));
  
  /*
    動態陣列的使用
  */

  free(dynamicIntArr); // 釋放 dynamicIntArr 所占用的記憶體
  ```
- 要注意的是，`malloc` 的回傳值型別為 `void*`，所以要記得將其強制轉型為目前需要的資料型態。

## 用一維陣列表示多維陣列
一個二維陣列確實可以用 `int[][]` 或 `int**` 紀錄，但是後者用起來就是有點麻煩。我們其實可以直接用 `int*` 代替 `int**`，也就是直接把二維陣列當作一維的來寫：
\[\begin{bmatrix}
    a&b&c\\
    d&e&f\\
    g&h&i
\end{bmatrix}\longrightarrow[\underbrace{a,b,c}_{\text{row 1}},\underbrace{d,e,f}_{\text{row 2}},\underbrace{g,h,i}_{\text{row 3}}]\]
其它維度的陣列寫法便依此類推。
（事實上，記憶體在紀錄 `int[][]` 的時候就是用這個方式儲存資料的。）

## 陣列在函式中的傳遞
前面說到，陣列本身就是一個指標，所以當函式使用一維陣列作為參數，我們並不需要告訴它這個陣列的長度是多少：
```c
// 以一維陣列作為參數
int sumOfArr (int arr[], int arrSize);
```
但是因為多維陣列在記憶體都是以一維的形式表達，編譯器需要知道其它維度的長度，才有辦法計算出目前的索引值：
```c
// 以二維陣列作為參數
int sumOfArr (int arr[][5], int row, int column);
```

## 陣列與稀疏矩陣
當矩陣內的大多元素皆為零，把所有的元素都存起來就顯得很沒效率。其實用元素型別為儲存行、列、元素值的 3-tuple 陣列表示就可以了。例如
\[\begin{bmatrix}
    1&0&0\\
    0&0&0\\
    0&3&0
\end{bmatrix}\]
即能以
\[\left[<0, 0, 1>, <2, 1, 3>\right]\]
表示。
