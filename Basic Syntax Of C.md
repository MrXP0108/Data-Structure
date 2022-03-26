# C的一些基礎語法

## `struct` 的相關使用
### `struct` 的基本宣告
```c
struct StuData {
    char name[10];
    int score;
};

struct StuData p[num];
struct StuData* p_ptr;
```

### 透過 `typedef` 將結構型別化
```c
typedef struct stuData {
    char name[10];
    int score;
} StuData, *StuData_Ptr;

StuData p[num];
StuData_Ptr p_ptr;
```
我們大可以連宣告內的 `stuData` 都省略掉，這會使得該結構體變為匿名。

### `struct` 與 `union` 的差別
不同於 `struct`，`union` 將同一份空間分給不同的資料內容。這適合用在整合相似的資料類型。

```c
// 舉例
typedef struct {
    int row, col, val;
} EntryNode;

typedef struct {
    MatrixNode_ptr down, right;
    nodeType tag;
    union u {
        MatrixNode_ptr next;
        EntryNode element;
    };
} MatrixNode, *MatrixNode_ptr;
```

## `string.h` 裡的常用函數
### `strcpy()` 複製字串、`strcat()` 接合字串
```c
#include <stdio.h>
#include <string.h>
 
int main () {
    char src[50], dest[50];

    strcpy(src,  "This is source");
    strcpy(dest, "This is destination");
    
    strcat(dest, src);
    
    printf("result: |%s|", dest);
    
    return 0;
}

// result: |This is destinationThis is source|
```

### `strcmp()` 比較字串
如果 `strcmp(str1, str2)` 的返回值為 `res`，則
\[\text{res}\begin{cases}
    >0\\
    =0\\
    <0
\end{cases}\implies\begin{cases}
    \text{str 1}>\text{str 2}\\
    \text{str 1}=\text{str 2}\\
    \text{str 1}<\text{str 2}
\end{cases}\]

### `strlen()` 給出字串長度
`strlen()` 的回傳值為 `size_t`。

## 靜態前綴詞 `static` 的應用
### 隱藏成員
在 C 中，當我們同時編譯兩個檔案 `A.c` 與 `B.c`，`A.c` 可以透過 `extern` 前綴詞來調用 `B.c` 的全域變數與函數。但是當全域變數或函數加了前綴詞 `static` 時，其它的支檔就無法調用該變數或函數。

### 記憶體的事先配置
當區域變數加了前綴詞 `static`，該變數會在程式開始運行時就將記憶體提早配置好，待執行到該處便直接進行實體化，且當程式離開了作用域仍會持續被保留，直到程式結束。

另外值得一提的是，在同一個函數內，只要區域變數加了前綴詞 `static`，那麼宣告時的初始賦值就只會進行一次：
```c
void alterNum () {
    static int num = 1;
    printf("%d ", num);
    num = 0;
}

int main () {
    alterNum(); // 1
    alterNum(); // 0
    return 0;
}
```

### 初始化變數值為 `0x00`
不管是稀疏矩陣還是字串的創建，使用前綴詞 `static` 都可以將它們的初始資料全部設為 `0x00`，這樣我們就不需要把矩陣的每一個元素都分別設為零，抑或是將字元陣列的最後一項另外設為 `\0`。
