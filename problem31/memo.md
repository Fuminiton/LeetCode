## 取り組み方
- step1: 5分以内に空で書いてAcceptedされるまで解く + テストケースと関連する知識を連想してみる
- step2: 他の方の記録を読んで連想すべき知識や実装を把握した上で、前提を置いた状態で最適な手法を選択し実装する
- step3: 10分以内に1回もエラーを出さずに3回連続で解く

## step1
`nums[index]`を使用したときのLISを`length_LIS[index]`とする

ある`index`とその`index`よりも前の`former_index`について、
`nums[former_index] < nums[index]`の時、`length_LIS[former_index] + 1`が`length_LIS[index]`の候補となる。
これを一通りみていって、`length_LIS`を更新していって最大のものが答えとなる。

```python
class Solution:
    def lengthOfLIS(self, nums: List[int]) -> int:
        length_nums = len(nums)
        length_LIS = [1] * length_nums

        for index in range(1, length_nums):
            for former_index in range(index):
                if nums[index] > nums[former_index]:
                    length_LIS[index] = max(
                        length_LIS[index],
                        length_LIS[former_index] + 1
                    )
        return max(length_LIS)
```

## step2
### 読んだコード
- https://github.com/fuga-98/arai60/pull/31/files
- https://github.com/sakupan102/arai60-practice/pull/32/files
- https://github.com/hayashi-ay/leetcode/pull/27/files

### 感想
- numsをはじめから見ていって、各長さのLISにおける末尾の最小値を保持するやり方もあった
  - 発想も素直な気がするのでこれは思いついても良さそうだった
  - ついでに`bisect_left`の実装をみたが、変数名が雑すぎるなあ
    - https://github.com/python/cpython/blob/3.13/Lib/bisect.py#L74
- 一方、末尾の最小値を保持していくやり方は、最終的なLIS自体を返してくれと追加の要件が出てきたときに編集しにくそうなので、動的計画法で実装する方が良さそう
- `segment tree`と座標圧縮を利用した解法もあるみたいだが、常識から外れそうなので一旦スキップする
- `LIS`は小文字でも良さそう

#### 動的計画法
```python
class Solution:
    def lengthOfLIS(self, nums: List[int]) -> int:
        length_lis = [1] * len(nums)
        for index in range(len(nums)):
            for former_index in range(index):
                if nums[index] > nums[former_index]:
                    length_lis[index] = max(
                        length_lis[index],
                        length_lis[former_index] + 1
                    )
        return max(length_lis)
```

#### 二分探索
```python
class Solution:
    def lengthOfLIS(self, nums: List[int]) -> int:
        # tails: 各長さの増加部分列における最小の末尾値を保持するリスト
        tails = []
        for num in nums:
            insertion_position = bisect_left(tails, num)            
            if insertion_position == len(tails):
                tails.append(num)
            else:
                tails[insertion_position] = num
        return len(tails)
```

## step3

```python
class Solution:
    def lengthOfLIS(self, nums: List[int]) -> int:
        length_lis = [1] * len(nums)
        for index in range(len(nums)):
            for former_index in range(index):
                if nums[index] > nums[former_index]:
                    length_lis[index] = max(
                        length_lis[index], length_lis[former_index] + 1)
        return max(length_lis)
```