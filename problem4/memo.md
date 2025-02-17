# はじめに
## 取り組み方
- step1: 5分以内に空で書いてAcceptedされるまで解く
- step2: discordとwebで検索した結果を踏まえ、コードの洗練を洗練させる + 何を悪いと思ったか、何を避けたいと思ったか等感じたことを記載する
- step3: 10分以内に1回もエラーを出さずに3回連続で解く

## step1
```python
class Solution:
    def deleteDuplicates(self, head: Optional[ListNode]) -> Optional[ListNode]:
        if head is None:
            return None
        """Place a dummy_node before head
        because the first node may be the target for removal"""
        dummy_node = ListNode(-111, head)
        previous_node = dummy_node
        current_node = dummy_node.next
        while current_node:
            next_node = current_node.next
            remove_required = False
            while next_node and current_node.val == next_node.val:
                next_node = next_node.next
                remove_required = True
            if remove_required:
                current_node = previous_node
                current_node.next = next_node 
            else:
                previous_node = previous_node.next
            current_node = current_node.next
        return dummy_node.next
```

### 思考過程
現在みているノードと1つ前と1つ後のノードを使いたい。
- 現在のノードが次のノードと値が同じならば、
  - 違う値になるまでノードを進める
  - 「1つ前のノード」と「現在と違う値に更新された次の値のノード」を繋げる
- 現在のノードが次のノードと値が違えば、
  - 「現在のノード」と「次のノード」を繋げる

ノードの更新の仕方は？
- 現在のノードが次のノードと値が同じならば、
  - 1つ前のノードと現在のノードは同じになるから更新はいらない
- 現在のノードが次のノードと値が違えば、
  - 1つ前のノードを進める

先頭から重複がある場合を考慮する必要があるため、一旦あり得ない値を入れたダミーのノードを用意したい。

### 感想
- 意味もなくフラグを複数回更新する点があまりイケてなさそう。
- あとダミーノードの値はなんでも良いはず。

## step2
```python
class Solution:
     def deleteDuplicates(self, head: Optional[ListNode]) -> Optional[ListNode]:
        def get_duplicates_removed_next(node: Optional[ListNode]) -> Optional[ListNode]:
            current_val = node.val
            while node.next and current_val == node.next.val:
                node = node.next
            return node.next

        if head is None:
            return None
        dummy_node = ListNode(0, head)
        previous_node = dummy_node
        current_node = dummy_node.next
        while current_node:
            next_node = current_node.next
            if next_node and current_node.val == next_node.val:
                current_node = previous_node
                current_node.next = get_duplicates_removed_next(next_node)
            else:
                previous_node = previous_node.next
            current_node = current_node.next
        return dummy_node.next
```

### 感想
- フラグを安直に使う姿勢はあまり良くなさそうなので、使わない方法を検討した。
- 連続して同じ値である時に、違う値になるまで進めたノードを返すという処理を関数化した。
- 変数が多くなりすぎている気がするが理解はしやすいと自分は感じたので、ここら辺はバランスなのか。

## step3
```python
class Solution:
    def deleteDuplicates(self, head: Optional[ListNode]) -> Optional[ListNode]:
        def get_val_change_node(node: Optional[ListNode]) -> Optional[ListNode]:
            node_val = node.val
            while node.next and node_val == node.next.val:
                node = node.next
            return node.next

        if head is None:
            return None
        dummy = ListNode(-1, head)
        current_node = dummy
        while current_node:
            next_node = current_node.next
            if next_node and next_node.next and next_node.val == next_node.next.val:
                next_node = get_val_change_node(next_node)
                current_node.next = next_node
            else:
                current_node = current_node.next
        return dummy.next
```
### 感想
- step3の途中で、初めからcurrent_nodeをdummyからはじめればシンプルに実装できることに気づいた。
  - 重複した塊の後に、別の重複した塊がくる点には注意した。
