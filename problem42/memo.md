## 取り組み方
- step1: 5分以内に空で書いてAcceptedされるまで解く + テストケースと関連する知識を連想してみる
- step2: 他の方の記録を読んで連想すべき知識や実装を把握した上で、前提を置いた状態で最適な手法を選択し実装する
- step3: 10分以内に1回もエラーを出さずに3回連続で解く

## step1
> All the integers of nums are unique.

全て同じ値の時はどうするのかと思ったが、重複は考えなくてよさそう。

回転された配列は昇順に並んだ配列を二つ並べたような形になっていて、2つ目の昇順部分の初めが返したい最小値。
また、2つ目の昇順部分の任意の要素は1つ目の昇順部分の任意の要素より小さい。

条件`nums[mid] <= nums[-1]`を満たす最小の`index`が常に`[left, right]`の範囲内にあるように更新していく。
この時、終了条件は、探索範囲が空で`left > right`となる時。
また、更新の仕方は

- 条件を満たす場合　　：`mid`より右側に最小の`index`はないので、`hi = mid - 1`として更新する
- 条件を満たさない場合：`mid`を含む左側の要素は条件を満たさないので、`lo = mid + 1`と更新する

そのほかに、`lo`未満の範囲の全ての要素が条件を満たさず、かつ、`hi`以上の範囲の全ての要素が条件を満たすという条件を維持して実装する方針と、nums[0]との大小関係を条件として使う実装する方針があるはず。

```python
class Solution:
    def findMin(self, nums: List[int]) -> int:
        if not nums:
            return -1
        if nums[0] < nums[-1]:
            return nums[0]
        
        lo = 0
        hi = len(nums) - 1

        while not lo > hi:
            mid = (lo + hi) // 2
            if nums[mid] <= nums[-1]:
                hi = mid - 1
            else:
                lo = mid + 1
        
        return nums[lo]
```

## step2
### 読んだコード
- https://github.com/seal-azarashi/leetcode/pull/39/files
- https://github.com/Mike0121/LeetCode/pull/44/files
- https://github.com/Yoshiki-Iwasa/Arai60/pull/35/files
- https://github.com/takuya576/leetcode/pull/2/files

### 感想
https://github.com/seal-azarashi/leetcode/pull/39/files#r1849419449

上記で言及されているものに答えられるか確認する。

> 「2で割る処理がありますがこれは切り捨てでも切り上げでも構わないのでしょうか。」

step1の実装ではどちらでも良い。
なぜなら`lo`、`hi`の更新がどちらも`mid±1`するようになっているので、`mid`が`lo`,`hi`のどちらによっていても、探索範囲が狭まっていくため。

逆に、`lo`未満の範囲の全ての要素が条件を満たさず、かつ、`hi`以上の範囲の全ての要素が条件を満たすという条件を維持して実装する方針では、切り捨てでなければならない。
なぜなら、`hi`の更新が条件を満たすように更新しないといけないために`hi=mid`と更新することになるので、
切り上げにすると探索範囲が2要素の時に`hi=mid`で更新し続けて無限ループするため。

> 「nums[middle] <= nums[right] とありますが、これは < でもいいですか。」

`<=`である必要がある。`False, Flase, ..., False, True, True, ..., True`となる探索範囲で切れ目を探したく、`<`にすると末端が`False`になるので。

> 「right の初期値は nums.length でもいいですか。」

良いが、`lo <= mid < hi`の中に`nums[mid] <= nums[-1]`を満たす最小の`index`を探すことになるので、終了条件と`hi`の更新方法を変える必要がある。


### bisect_left
```python
class Solution:
    def findMin(self, nums: List[int]) -> int:
        first_index = bisect_left(
            nums, True, key = lambda num: num <= nums[-1])
        return nums[first_index]
```

### `lo <= mid < hi`で`nums[mid] <= nums[-1]`を満たす最小の`index`を探す
```python
class Solution:
    def findMin(self, nums: List[int]) -> int:
        if not nums:
            return -1
        if nums[0] < nums[-1]:
            return nums[0]
        
        lo = 0
        hi = len(nums)

        while lo < hi:
            mid = (lo + hi) // 2
            if nums[mid] <= nums[-1]:
                hi = mid
            else:
                lo = mid + 1
        
        return nums[lo]
```

### `lo <= mid <= hi`で`nums[mid] <= nums[-1]`を満たす最小の`index`を探す
```python
class Solution:
    def findMin(self, nums: List[int]) -> int:
        if not nums:
            return -1
        if nums[0] < nums[-1]:
            return nums[0]
        
        lo = 0
        hi = len(nums) - 1

        while lo <= hi:
            mid = (lo + hi + 1) // 2
            if nums[mid] <= nums[-1]:
                hi = mid - 1
            else:
                lo = mid + 1
        
        return nums[lo]
```

## step3
```python
class Solution:
    def findMin(self, nums: List[int]) -> int:
        if not nums:
            return -1
        if nums[0] < nums[-1]:
            return nums[0]
        
        lo = 0
        hi = len(nums)

        while lo < hi:
            mid = (lo + hi) // 2
            if nums[mid] <= nums[-1]:
                hi = mid
            else:
                lo = mid + 1
        
        return nums[lo]
```