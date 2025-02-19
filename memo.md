# はじめに
## 取り組み方
- step1: 5分以内に空で書いてAcceptedされるまで解く
- step2: discordとwebで検索した結果を踏まえ、コードの洗練とコメントの予測を行う
- step3: 10分以内に1回もエラーを出さずに3回連続で解く

# step1
## 実装
```python
class Solution:
    def detectCycle(self, head: Optional[ListNode]) -> Optional[ListNode]:
        visited = set()
        current_node = head
        while current_node:
            if current_node in visited:
                return current_node
            visited.add(current_node)
            current_node = current_node.next
        return None
```
## 感想
- Linked List Cycle 1 と出力が違うだけでやるべきことは同じ。

# step2
## 実装
```python
class Solution:
    def detectCycle(self, head: Optional[ListNode]) -> Optional[ListNode]:
        visited = set()
        current_node = head
        while True:
            if current_node is None:     # Reached the tail
                return None
            if current_node in visited:  # Cycle detected
                return current_node
            visited.add(current_node)
            current_node = current_node.next
```
## 感想
- 無限ループで書くと、サイクルを検出できた状態とできなかった状態が同じインデント上に並ぶので分かりやすいかと思ったので、そう書いてみた。

# step3
## 実装
```python
class Solution:
    def detectCycle(self, head: Optional[ListNode]) -> Optional[ListNode]:
        visited = set()
        current_node = head
        while True:
            if current_node is None:  # Reached the tail node
                return None
            elif current_node in visited:  # Cycle detected
                return current_node
            else:  # Move to the next
                visited.add(current_node)
                current_node = current_node.next
```
## 感想
- step2の考え方を応用して、3状態に分けて考えるのも良いかと思った。
