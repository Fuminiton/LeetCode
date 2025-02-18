# はじめに
## 取り組み方
- step1: 5分以内に空で書いてAcceptedされるまで解く
- step2: discordとwebで検索した結果を踏まえ、コードの洗練を洗練させる + 何を悪いと思ったか、何を避けたいと思ったか等感じたことを記載する
- step3: 10分以内に1回もエラーを出さずに3回連続で解く

## step1
```python
class Solution:
    def addTwoNumbers(self, l1: Optional[ListNode], l2: Optional[ListNode]) -> Optional[ListNode]:
        def get_val(list_node: Optional[ListNode]) -> int:
            if list_node:
                return list_node.val
            else:
                return 0

        def get_next(list_node: Optional[ListNode]) -> Optional[ListNode]:
            if list_node:
                return list_node.next
            else:
                return None
        
        carry = 0
        added = ListNode()
        node = added

        while l1 or l2 or carry:
            l1_val = get_val(l1)
            l2_val = get_val(l2)
            digit = (l1_val + l2_val + carry) % 10
            carry = (l1_val + l2_val + carry) // 10
            node.next = ListNode(digit)
            node = node.next
            l1 = get_next(l1)
            l2 = get_next(l2)
        
        return added.next
```

### 思考過程
l1, l2 が None の時は 0 としたい
繰上げ の初期値は 0 
桁ごとの足し算 が % 10 で繰上げが // 10 すれば良い
### 感想
- l1とl2に同じ操作をする部分は関数化した
- 割り算して小数点にならないかだけ気になったので調べる

## step2
```python
class Solution:
    def addTwoNumbers(self, l1: Optional[ListNode], l2: Optional[ListNode]) -> Optional[ListNode]:
        def get_val(list_node: Optional[ListNode]) -> int:
            return list_node.val if list_node else 0

        carry = 0
        added = ListNode()
        node = added
        while l1 or l2 or carry:
            total = get_val(l1) + get_val(l2) + carry
            digit = total % 10
            carry = total // 10
            node.next = ListNode(digit)

            node = node.next
            if l1:
                l1 = l1.next
            if l2:
                l2 = l2.next
        return added.next
```
### 感想
- l1, l2の更新はあまりに単純なので関数化しなくても良いかと思った

## step3
```python
class Solution:
    def addTwoNumbers(self, l1: Optional[ListNode], l2: Optional[ListNode]) -> Optional[ListNode]:
        def get_val(list_node: Optional[ListNode]) -> int:
            if list_node:
                return list_node.val
            else:
                return 0
        
        carry = 0
        dummy = ListNode()
        node = dummy
        while l1 or l2 or carry:
            total = get_val(l1) + get_val(l2) + carry
            digit = total % 10
            carry = total // 10
            node.next = ListNode(digit)
            node = node.next
            if l1:
                l1 = l1.next
            if l2:
                l2 = l2.next
        return dummy.next
```
