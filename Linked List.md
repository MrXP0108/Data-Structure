# 鏈結串列

## 如何找到下一個節點
刪除節點 `n` 的作法即為將 `n` 的前一個節點 `PreNode` 指向 `n` 的後一個節點。

很顯然地，單向鏈結串列的第一個節點不存在 `PreNode`。以下為 `PreNode()` 與 `deleteNode()` 的實作：
```c
Node_ptr PreNode (Node_ptr head, Node_ptr target) {
    Node_ptr w = head;

    while (w != NULL && w->next != target)
        w = w->next;

    return w;
}

void deleteNode (Node_ptr head, Node_ptr target) {
    Node_ptr w = PreNode(head, target);
    
    if (!w)
        head = head->next;
    else
        w->next = target->next;

    free(target);
}
```

## 單向環狀鏈結串列的回收
若 `head` 為一單向環狀串列的起始節點，而 `avail` 為預備串列的起始節點，那麼
1. `head->next = avail;`
2. `avail = head->next;`

便能直接回收整個串列。相對於非環狀的單向串列需要線性的時間複雜度，單向環狀串列的 `O(1)` 解顯然是更好的。

## 單向鏈結串列的反向
```c
Node_ptr invertList (Node_ptr head) {
    Node_ptr newHead = NULL, newHeadNext = NULL;
    
    while (head) {
        newHeadNext = newHead;
        newHead = head;
        head = head->next;
        
        newHead->next = newHeadNext;
    }

    return newHead;
}
```