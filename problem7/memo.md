# はじめに
## 取り組み方
- step1: 5分以内に空で書いてAcceptedされるまで解く
- step2: discordとwebで検索した結果を踏まえ、コードの洗練を洗練させる + 何を悪いと思ったか、何を避けたいと思ったか等感じたことを記載する
- step3: 10分以内に1回もエラーを出さずに3回連続で解く

## step1
```python
class Solution:
    def reverseList(self, head: Optional[ListNode]) -> Optional[ListNode]:
        prev_node = None
        current_node = head
        while current_node:
            next_node = current_node.next
            current_node.next = prev_node
            prev_node = current_node
            current_node = next_node
        return prev_node
```

### 思考過程
- スタックにデータを積みきった値をpopしつつ連結リストを繋いでいくか、連結リストの向き先を変えていくやり方がありそう
- スタックを持つ必要がないため、後者を選択した
- 1つ前、現在、1つ後のノードをみて、現在の次のノードの向き先を1つ前に向かせて、処理を前に進めていくイメージで実装する

### 感想
- prev_nodeという命名があまり意味を成していないように感じた。

## step2
```python
class Solution:
    def reverseList(self, head: Optional[ListNode]) -> Optional[ListNode]:
        reversed_node = None
        original_node = head
        while original_node:
            next_node = original_node.next
            original_node.next = reversed_node
            reversed_node = original_node
            original_node = next_node
        return reversed_node
```
### 感想
- reversed、originalという命名に変えて多少分かりやすくなった気がする。

## step3
```python
class Solution:
    def reverseList(self, head: Optional[ListNode]) -> Optional[ListNode]:
        node = head
        stack = []
        while node:
            stack.append(node.val)
            node = node.next
        dummy = ListNode()
        reversed = dummy
        while stack:
            reversed.next = ListNode(stack.pop())
            reversed = reversed.next
        return dummy.next
```
### 感想
- 直近で linked list を扱っていたから、逆順に連結リストを付け替えていく方法が分かりやすかっただけで、実際はスタックを用いて実装する方が自然なのかと思ったので、そうした。
