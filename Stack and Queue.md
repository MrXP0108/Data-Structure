# 堆疊與佇列

## 堆疊的相關運算
- `push(s, item)`：將 `item` 放入 `s` 的頂端。
- `pop(s)`：將 `s` 頂端的項目回傳並刪除。
- `isEmpty(s)`
- `isFull(s)`
- `peek(s)`：與 `pop(s)` 不同，它只回傳 `s` 頂端的項目回傳，但並不會刪除之。

## 佇列的相關運算
- `enqueue(q, item)`：將 `item` 放入 `q` 的尾端。
- `dequeue(q)`：將 `q` 前端的項目回傳並刪除。
- `isEmpty(q)`
- `isFull(q)`

## 堆疊的實作
```c
typedef struct {
    int val;
    // ...
} Element;

typedef struct {
    Element stack[100];
    int top;
} Stack;
```
`top` 指的是目前頂端項目所在的索引值，故宣告新的堆疊時要記得先將其設為 `-1`。
```c
// 若 isFull(q) == 0 的 push 程式碼
S->stack[++S->top] = item;

// 若 isEmpty(q) == 0 的 pop 程式碼
return S->stack[S->top--];
```

## 環狀佇列的實作
依照佇列的定義，陣列在儲存一般的型態的佇列時，往往會因為進行多次的 `enqueue()` 與 `dequeue()` 而使得佇列最後無法存取任何資料。我們可以透過將佇列環狀化來解決這個問題。

我們首先將表示前後端索引值的 `front` 與 `rear` 設為 `0`。要注意的是，`front` 表示的是第一個項目前的位置，而 `rear` 則表示末項目的位置。

我們可以發現：`front` 與 `rear` 的定義使得 `isEmpty(q) == true` 與 `isFull(q) == true` 的成立條件皆為 
```c
q.front == q.rear
```

而因為環狀佇列的特性，我們不能直接寫 `CQ->cqueue[++CQ->rear]` 與 `return CQ->cqueue[CQ->front--]`，而應該如以下程式碼定義 `enqueue()` 與 `dequeue()`：
```c
void enqueue_CQ (CQueue_ptr CQ, Element item) {
    // 避免索引值超出範圍
    CQ->rear = (CQ->rear + 1) % max_size;
    
    if (isFull(*CQ))
        printf("Queue already full!");
    else
        CQ->cqueue[CQ->rear] = item;
}

Element dequeue_CQ (CQueue_ptr CQ) {
    if (isEmpty(*CQ)) {
        printf("Nothing to be dequeued!");
        return NULL;
    }
    else {
        // 避免索引值超出範圍
        CQ->front = (CQ->front + 1) % max_size;
        return CQ->cqueue[CQ->front];
    }
}
```

## 後序表示法與堆疊
對人類而言，中序表示法是一個較為直觀的表示法，但是它對電腦其實是相當難以理解的。也就是說，相較於中序的 `(a + b) * (c + d)`，電腦其實會先將其轉為後序的 `a b + c d + *`。

對後序表示法而言，運算式的讀取只需要 `O(n)` 的時間複雜度：
```c
// 讀取運算式的虛擬碼

for (int i = 0; i < length; ++i) {
    if (expr[i] is an operand)
        push(stack, expr[i]);
    else {
        a = pop(stack);
        b = pop(stack);
        push(stack, operated a and b);
    }
}
pop(stack);
```
我們同樣也可以使用堆疊來將一個中序表示法後序化。要注意的是，因為不同的運算子具有不同的優先順序，堆疊下層的運算子一定要為優先度低者，而上層的運算子則必定具有較高的優先度。

優先度又分為堆疊外優先度 (incoming precedence, `icp`) 與堆疊內優先度 (in stack precedence, `isp`) ：`icp` 決定 `push(stack, operator)` 的運作與否；`isp` 決定 `pop(stack, operator)` 的運作與否，以及其運作的停止條件。以左括弧 `(` 為例，`(` 一定可以被 `push` 進堆疊，但絕對不能 `pop` 出任何東西。
```c
typedef enum {
    LPAREN, RPAREN, PLUS, MINUS, TIMES, DIVIDE, EOE, OPERAND
} TokenType;

// ...

int isp[] = { 0, 19, 12, 12, 13, 13, 0};
int icp[] = {20, 19, 12, 12, 13, 13, 0};
TokenType stack[stack_max_size];
char expr[expr_max_size];

// ...

char symbol;
TokenType token;
int top = 0, c = 0;
stack[0] = EOE;

do {
    token = GetToken(&symbol, &c);
    if (token == OPERAND)
        printf("%c", symbol);
    else {
        if (token == RPAREN) {
            while (stack[top] != LPAREN)
                PrintTokenOperator(pop(stack, &top));
            pop(stack, &top); // 不會印出括號
        }
        else {
            while (isp[stack[top]] >= icp[token])
                PrintTokenOperator(pop(stack, &top));
            push(stack, &top, token);
        }
    }
} while (token != EOE);

while ((token = pop(stack, &top)) != EOE)
    PrintTokenOperator(token);
```