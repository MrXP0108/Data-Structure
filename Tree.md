# 樹狀結構

## 左子右弟表示法
左子右弟表示法其實就是將任意一棵樹轉換為二元樹的方法：
```c
typedef struct {
    Element val;
    NodeLCRS_ptr child;
    NodeLCRS_ptr sibling;
} NodeLCRS, *NodeLCRS_ptr;
```

## 二元樹的特性
- 二元樹可以是空的。
- 二元樹的子數具有順序性。
- 第 `i` 層的節點個數最多有 \(2^{i-1}\) 個。
- 深度為 `k` 的二元樹最多有 \((2^{k}-1)\) 個節點。

## 特殊的二元樹
- 傾斜樹：分為左斜樹和右斜樹。
- 完全二元樹：又稱為滿枝二元樹，意指高度為 `k` 且具有 \((2^{k}-1)\) 個節點的二元樹。
- 完整二元樹：意指節點編號與滿枝二元樹完全一致的二元樹。

根據定義，滿枝二元樹與完整二元樹皆適合以用陣列循序儲存，因為不會有任何的空間被浪費，而傾斜樹則否。

## 完整二元樹的特性
- 其高度為 \(\lfloor\text{log}_2n + 1\rfloor\)。
- 節點 `i` 的父節點為節點 \(\lfloor i/2\rfloor\)，其中 \(i\neq 1\)。
- 若 \(2i\leq n\)，則節點 `i` 的左子節點為節點 `2i`。
- 若 \(2i > n\)，則節點 `i` 不具有左子節點。
- 若 \(2i+1\leq n\)，則節點 `i` 的右子節點為節點 `2i + 1`。
- 若 \(2i+1 > n\)，則節點 `i` 不具有右子節點。

## 二元樹的走訪
### 走訪的種類
因為二元樹的優先順序為左大於右，故走訪二元樹的方法只有三種：
- 中序走訪 (LDR)：左子樹 &rarr; 根節點 &rarr; 右子樹。
- 前序走訪 (DLR)：根節點 &rarr; 左子樹 &rarr; 右子樹。
- 後序走訪 (LRD)：左子樹 &rarr; 右子樹 &rarr; 根節點。

我們可以透過走訪兩棵二元樹來確定它們是否相同：若它們的前序與中序走訪值相同，抑或是它們的後序與中序走訪值相同，則這兩棵二元樹相同。

### 以迭代法實作二元樹的走訪
以遞迴法實作二元樹的走訪既簡單又直觀：
```c
void LDR_recursion (BT_ptr root) {
    if (bt) {
        LDR(root->lc);
        printf("%d ", root->val);
        LDR(root->rc);
    }
}
```
但是過多的遞迴會消耗大量的堆疊空間，因此迭代法是一個較好的選擇：
```c
void LDR_interation (BT_ptr root) {
    while (1) {
        while (root) {
            push(stack, &top, root);
            root = root->lc;
        }
        root = pop(stack, &top);
        if (!root) break;
        printf("%c ", root->val);
        root = root->rc;
    }
}
```

## 引線二元樹
### 引線二元樹的優點
因為二元樹節點的 `lc` 與 `rc` 皆可能為空指標，也就是指向 `NULL`，我們可以將部分鏈結替換為引線：引線分為左引線與右引線，左引線連接中序表示法的上一個節點，左引線則連接中序表示法的下一個節點。

為了區別鏈結與引線別，我們以兩個布林變數 `isLT`、`isRT`，決定一個節點的指標為何種類型。而因為中序表示法的最左與最右者的 `isLT` 與 `isRT` 分別為 `1`，且其 `lc` 與 `rc` 分別為 `NULL`，我們需要另外創建一個標頭節點：
```c
BT_ptr head = (BT_ptr)malloc(sizeof(BT));
head->isLT = head->isRT = 0;
head->lc = root;
head->rc = head;
```
引線可以幫助我們更快找到一個節點在中序表示法的上一個與下一個節點，進而提升走訪二元樹的效率：
```c
BT_ptr LDRnext (BT_ptr root) {
    BT_ptr temp = root->rc;

    if (!temp->isRT)
        while (!temp->isLT)
            temp = temp->lc;
    
    return temp;
}

BT_ptr BTtraversal (BT_ptr root) {
    BT_ptr temp = root;
    while (1) {
        temp = LDRnext(temp);
        if (temp == root) break;
        print("%c ", temp->val);
    }
}
```
<!--### 引線二元樹的修改：加入節點-->

### 將林轉換為二元樹
林即為多個互斥樹所形成的集合，其值可以為一空集合。而將林轉換為二元樹只需要以下的兩個步驟：
1. 將所有的樹轉換為二元樹。
2. 將所有樹的根節點連在一起：`root_1` &rarr; `root_2` &rarr; `root_3` &rarr; &sdot;&sdot;&sdot; &rarr; `root_n`。

## 累堆
累堆為完整二元樹的一個特例，它常被用來實作優先權佇列。
### 優先權佇列
顧名思義，優先權佇列不一定具有 FIFO 的特性，它允許「插隊」的存在。也就是說，優先權佇列在處理作業的順序時，會先考慮優先度的大小，再考慮加入時間的先後。

很顯然地，一般的佇列結構並不支援這樣的運算方式，因此我們需要最大累堆的幫助。

### 最大樹與最大累堆
若一個位於最大樹的節點具有子節點，則其鍵值必定不小於其所有子節點的鍵值。而最大累堆即為完整二元樹與最大樹的交集。

因為最大累堆也是一棵完整二元樹，故我們可以直接用陣列儲存之。

### 在最大累堆中增加項目
```c
void insertInMaxHeap (Element item, int* n) {
    if (maxHeapIsFull(*n)) {
        printf("Max Heap is full!");
        return;
    }

    int i = ++(*n); // 長度增加一，代表要填入新項目
    while (i != 1) {
        // 最大樹的定義
        if (item.key <= maxHeap[i / 2].key) break;

        heap[i] = heap [i / 2];
        i /= 2;
    }
    maxHeap[i] = item;
}
```

### 在最大累堆中刪除項目
對最大累堆而言，刪除它的一個項目即等同於取出鍵值最大的元素。而為了使其符合完整二元樹的定義，我們首先必須將最大累堆的末項目移動到首項目的位置。
```c
Element deleteInMaxHeap (int* n) {
    if (maxHeapIsEmpty(*n)) {
        printf("Max heap is empty!");
        return;
    }

    Element item = maxHeap[1],
            temp = maxHeap[(*n)--];
    int parent = 1,
        child  = 2;
    while (child <= *n) {
        if (child < *n && maxHeap[child].key < heap[child + 1].key)
            ++child;
        if (temp.key >= maxHeap[child].key)
            break;

        // 上下節點互換
        maxHeap[parent] = maxHeap[child];

        // 移到下一層
        parent = child;
        child *= 2;
    }
    heap[parent] = temp;
    return item;
}
```

## 二元搜尋樹
### 二元搜尋樹的特性
對二元搜尋樹的任意一個節點 `node` 而言，若其左子節點 `lc` 與右子節點 `rc` 存在，則 `lc.key < node.key < rc.key` 必成立。根據這項定義，二元搜尋樹的任何兩個節點都不可能具有相同的鍵值，且該樹的中序表示法必定會是鍵值由小排列到大的結果。

### 在二元搜尋樹中增加項目
項目的增加其實就是沿著樹搜尋，直到 `NULL` 處時插入新節點：
```c
void insertInBST (BST_ptr root, int newVal) {
    BST_ptr newNode = (BST_ptr)malloc(sizeof(BST));
    newNode->val = newVal;
    newNode->lc = NULL;
    newNode->rc = NULL;
    
    // 檢查是否為空樹
    if (root == NULL) {
        root = newNode;
        return;
    }
    
    BST_ptr w = root;
    
    // 搜尋
    while (w) {
        // 檢查鍵值是否重複
        if (newVal == w->val) {
            printf("Val has repeated!");
            return;
        }

        if (newVal < w->val) {
            if (!w->lc) break;
            w = w->lc;
        }
        else {
            if (!w->rc) break;
            w = w->rc;
        }
    }

    // 插入
    if (newVal < w->val)
        w->lc = newNode;
    else
        w->rc = newNode;
}
```

### 在二元搜尋樹中刪除項目
```c
void deleteInBST (BST_ptr root, int target) {
    // 檢查是否為空樹
    if (root == NULL) {
        printf("Tree is empty!");
        return;
    }
    
    BST_ptr w = root;
    
    // 搜尋
    while (target != w->val) {
        if (!w) {
            printf("Target not found!");
            return;
        }
        
        if (newVal < w->val)
            w = w->lc;
        else
            w = w->rc;
    }

    if (!w->lc && !w->rc)
        w = NULL;
    else if (w->lc && !w->rc)
        w = w->lc;
    else if (!w->lc && w->rc)
        w = w->rc;
    else
        

    free(w);
}
```