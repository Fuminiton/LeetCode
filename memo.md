# はじめに
## 取り組み方
- step1: 5分以内に空で書いてAcceptedされるまで解く
- step2: discordとwebで検索した結果を踏まえ、コードの洗練とコメントの予測を行う
- step3: 10分以内に1回もエラーを出さずに3回連続で解く

# step1
## 実装
```python
class Solution:
    def deleteDuplicates(self, head: Optional[ListNode]) -> Optional[ListNode]:
        node = head
        while node:
            if node.next is None:
                break
            elif node.val == node.next.val:
                node.next = node.next.next
            else:
                node = node.next
        return head
```
## 思考過程
- 先頭からノードを見ていく
- 現在のノードの値と次のノードの値が重複しているならば、次にくるノードを次の次にくるノードに上書く、そうでない場合は、ノードを進める
- リストが1つの時、2つの時で問題ないか確認しておきたい。
## 感想
- Constraints で与えられる連結リストが空の場合があることを見落としてしまった。次からは入力の境界値を必ず確認する。
- また、↑のケースの考慮の仕方も改善の余地がありそうなので、step2で良い方法がないか確認する。

# step2
## 実装
```python
class Solution:
    def deleteDuplicates(self, head: Optional[ListNode]) -> Optional[ListNode]:
        if head is None:
            return head
        current_node = head
        while current_node:
            next_node = current_node.next
            # Move to the point of no duplication
            while (next_node) and (current_node.val == next_node.val):
                next_node = next_node.next
            current_node.next = next_node  # Update current_node.next
            current_node = current_node.next  # Move to next_node
        return head
```
## 感想
- discord上で「current.next」は次の処理へ進むとき以外に使うのは分かりづらい旨の議論があり、共感したのでnext_nodeを定義していくやり方を再検討した。
- また、早期リターンなる概念を知った。入力がある前提で話を進めた方が良いケースはあると思うので、積極的に使いたい。

# step3
## 実装
```python
class Solution:
    def deleteDuplicates(self, head: Optional[ListNode]) -> Optional[ListNode]:
        if head is None:
            return head
        current_node = head
        while current_node:
            next_node = current_node
            """Replace the next node with a node
            that does not duplicate the current node"""
            while (next_node) and (current_node.val == next_node.val):
                next_node = next_node.next
            current_node.next = next_node

            current_node = current_node.next
        return head
```
## 感想
- 絵に起こした時にすんなり理解できる処理の流れにできたと思う。
