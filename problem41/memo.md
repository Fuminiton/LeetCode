## 取り組み方
- step1: 5分以内に空で書いてAcceptedされるまで解く + テストケースと関連する知識を連想してみる
- step2: 他の方の記録を読んで連想すべき知識や実装を把握した上で、前提を置いた状態で最適な手法を選択し実装する
- step3: 10分以内に1回もエラーを出さずに3回連続で解く

## step1
target以上になるindexを二分探索で返せば良い。

leftが最終的にtargetを超える最初のインデックスになる、もしくはmidがtargetと一致して打ち切りするようにする。
探索範囲を`[left, right]`とするパターンと、`[left, right)`とするパターンで書く。

また、midの生成方法はleft + right // 2で切り捨て固定。
ループの終了条件は探索範囲が空の時、更新時はmidを2度と使わない(必ず範囲を狭める)ようにする。

テストケースとして考えたいのは
- sortされていないnums: [5,4], 1 <- 一旦、このケースはなしとする。
- 空配列: [], 1
- 単一要素: [5], 1
- 重複要素を含むnums: [5,5], 1
- 正常系: 奇数個のnums: [1,2,3], 5
- 正常系: 偶数個のnums: [1,2,3,4], 5

### 探索範囲を`[left, right]`とするとき
- 終了条件は探索範囲が空の時なので、left > right
- 更新方法は
  - nums[mid]がtarget　　　　　 : return left
  - nums[mid]がtargetより小さい : left = mid + 1
  - nums[mid]がtargetより大きい : right = mid - 1

```python
class Solution:
    def searchInsert(self, nums: List[int], target: int) -> int:
        if not nums:
            return 0

        # search [left, right]
        left = 0
        right = len(nums) - 1

        while left <= right:
            mid = (left + right) // 2
            if nums[mid] == target:
                return mid
            if nums[mid] < target:
                left = mid + 1
            if nums[mid] > target:
                right = mid - 1

        return left
```

### 探索範囲を`[left, right)`とするとき
- 終了条件は探索範囲が空の時なので、left >= right
- 更新方法は
  - nums[mid]がtarget　　　　　 : return left
  - nums[mid]がtargetより小さい : left = mid + 1
  - nums[mid]がtargetより大きい : right = mid

```python
class Solution:
    def searchInsert(self, nums: List[int], target: int) -> int:
        if not nums:
            return 0
        
        # search [left, right)
        left = 0
        right = len(nums)

        while left < right:
            mid = (left + right) // 2
            if nums[mid] == target:
                return mid
            if nums[mid] < target:
                left = mid + 1
            if nums[mid] > target:
                right = mid

        return left
```

## step2
### 読んだコード
- https://github.com/seal-azarashi/leetcode/pull/38/files
- https://github.com/Yoshiki-Iwasa/Arai60/pull/34/files
- https://github.com/nittoco/leetcode/pull/28/files
- https://github.com/hayashi-ay/leetcode/pull/40/files

### 感想
- leftをtarget以上になる初めての値になるように更新しているので、`if nums[mid] > target:`の条件にイコールを含めて、`if nums[mid]==target`の条件をなくしても成立する
- 探索範囲を決めたら、あとは無限ループをせず探索範囲を正しく狭められるように、終了条件、更新方法、切り捨てor切り上げを選択するくらいの理解度で実装しているが問題ないのだろうか
- 探索範囲を`(l, r]`、`(l, r)`であったり、`mid`に切り上げを使うのは直感に反する感じがする
  - `(l, r]`はスライスの定義の仕方と逆なので不自然な感じがする
  - `(l, r)`は`mid`が`-1`になる可能性を秘めていそうで抵抗感がある
  - 切り上げを使うのは、leftを返す話なのに、あえて更新が右寄りになるようにするところが変な感じ

- `bisect_left`の実装は、`[lo, hi)`で探索をし、`mid`には切り捨てを使っていた

https://github.com/python/cpython/blob/cfbdce72083fca791947cbb18114115c90738d99/Lib/bisect.py#L94

```python
class Solution:
    def searchInsert(self, nums: List[int], target: int) -> int:
        if not nums:
            return 0
        
        lo = 0
        hi = len(nums)

        while lo < hi:
            mid = (lo + hi) // 2
            if nums[mid] < target:
                lo = mid + 1
            else:
                hi = mid

        return lo
```

## step3
```python
class Solution:
    def searchInsert(self, nums: List[int], target: int) -> int:
        if not nums:
            return 0
        
        lo = 0
        hi = len(nums) - 1

        while not lo > hi:
            mid = (lo + hi) // 2
            if nums[mid] < target:
                lo = mid + 1
            else:
                hi = mid - 1
        
        return lo
```

### step4
暗記で書いてないか確かめるために`(lo, hi)`で探索するパターンも書いてみる

```python
class Solution:
    def searchInsert(self, nums: List[int], target: int) -> int:
        if not nums:
            return 0
        
        lo = -1
        hi = len(nums)

        while not hi - lo < 2:
            mid = (lo + hi) // 2
            if nums[mid] < target:
                lo = mid
            else:
                hi = mid

        return lo + 1
```