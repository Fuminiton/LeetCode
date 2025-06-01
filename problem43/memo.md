## 取り組み方
- step1: 5分以内に空で書いてAcceptedされるまで解く + テストケースと関連する知識を連想してみる
- step2: 他の方の記録を読んで連想すべき知識や実装を把握した上で、前提を置いた状態で最適な手法を選択し実装する
- step3: 10分以内に1回もエラーを出さずに3回連続で解く

## step1
numsを前半（末尾の数より大きい部分）と後半（末尾の数以下の部分）に分け、
targetが末尾より大きい場合は、前半を探して、targetが末尾以下なら後半を探す。

後半の開始を返す関数と、与えられたnumsの範囲にtargetがあるかを探す関数を用意する方針で実装する。
エッジケースとして考えたいのは、

- ソートされたものが回転されていない場合 -> これは今回対応しない
- 全て末尾以下の数になる場合
- 重複したnumがある

```py
class Solution:
    def search(self, nums: List[int], target: int) -> int:
        def search_min_index() -> int:
            if nums[0] < nums[-1]:
                return 0
            left = 0
            right = len(nums)
           
            while left < right:
                mid = (left + right) // 2
                if nums[mid] > nums[-1]:
                    left = mid + 1 
                else:
                    right = mid
            return left

        def search_target_index(left: int, right: int) -> int:
            while left < right:
                mid = (left + right) // 2
                if nums[mid] == target:
                    return mid
                elif nums[mid] < target:
                    left = mid + 1
                else:
                    right = mid
            return -1

        min_index = search_min_index()
        if min_index == 0:
            return search_target_index(0, len(nums))
        if target > nums[-1]:
            return search_target_index(0, min_index)
        else:
            return search_target_index(min_index, len(nums))
```

## step2
### 読んだコード
- https://github.com/sakupan102/arai60-practice/pull/44/files
- https://github.com/fhiyo/leetcode/pull/44/files
- https://github.com/fuga-98/arai60/pull/43/files
- https://discord.com/channels/1084280443945353267/1233295449985650688/1239594872697262121

### 感想
- ソート順を定義して1回の二分探索で解く方法もあったが、個人的には課題を分解して2回の二分探索を行う方が自然な操作に思えた
  - とはいえ、探索順を再定義するという発想は役に立ちそうな気もする
- 2つ目の二分探索の処理から求めたいindex or -1を返す方法の選択肢もいくつかあったが、今の書き方でも「回転されていない配列(or 360度*整数の回転)の対応をしていること」や「targetがどちらに属するかでどう処理を変えるか」が伝わりやすいので良いかとも思った
- step1の実装だと、targetが複数あったときに、規則を持ったindexを返せない構造になっているので、一番左のindexを返すor右を返すようにした方が汎用性が高そう

### step1の改良
- targetが複数あったときに、左端を返すように改良
- numsが空のケースを弾く

```py
class Solution:
    def search(self, nums: List[int], target: int) -> int:
        def search_min_index() -> int:
            if nums[0] <= nums[-1]:
                return 0
            left = 0
            right = len(nums)
            while left < right:
                mid = (left + right) // 2
                if nums[mid] > nums[-1]:
                    left = mid + 1
                else:
                    right = mid
            return left
        
        def search_target_index(left: int, right: int) -> int:
            while left < right:
                mid = (left + right) // 2
                if nums[mid] < target:
                    left = mid + 1
                else:
                    right = mid
            if left < len(nums) and nums[left] == target:
                return left
            return -1
        
        if nums is None:
            return -1
        min_index = search_min_index()
        if min_index == 0:
            return search_target_index(0, len(nums))
        if target > nums[-1]:
            return search_target_index(0, min_index)
        else:
            return search_target_index(min_index, len(nums))
```


### 1回の二分探索
FFFFTTTとなる規則を作って、境界を見つける二分探索の問題として捉える。
規則の考え方は、targetとnums[-1]の位置関係で場合分けし、

- 前半の山にtargetがあると予想される時は、numが前半にいるときはtargetとの大小関係を見る必要があり、後半は全てTrueとして返す
- 後半の山にtargetがあると予想される時は、numが前半にいるときは全てFaelseとして返し、後半は大小関係を見る必要がある

のように考えれば良い。

```py
class Solution:
    def search(self, nums: List[int], target: int) -> int:
        def is_in_target_range(num: int) -> bool:
            if nums[-1] < target:  # targetが前半の山にいると思われる
                if nums[-1] < num:  # numが前半の山にいる
                    return target <= num
                else:  # numが後半の山にいる
                    return True
            if target <= nums[-1]:  # targetが後半の山にいると思われる
                if nums[-1] < num:  # numが前半の山にいる
                    return False
                else:  # numが後半の山にいる
                    return target <= num

        left = 0
        right = len(nums)
        while left < right:
            mid = (left + right) // 2
            if is_in_target_range(nums[mid]):
                right = mid
            else:
                left = mid + 1
        if left < len(nums) and target == nums[left]:
            return left
        return -1
```

### 1回の二分探索をさらに工夫したもの
(numが後半の山に属するか、numがtarget以上か)というタプルについて考えると、

1. numが前半の山に属し、target未満  -> (False, False)
2. numが前半の山に属し、target以上  -> (False, True)
3. numが後半の山に属し、target未満  -> (True, False)
4. numが後半の山に属し、target以上  -> (True, True)

のように辞書順でソートされる。
また、bisect_left が 二つの辞書順を考慮した以下のような二分探索ができる。

```py
data = [(1, 'a'), (1, 'b'), (2, 'a'), (2, 'c'), (3, 'a')]
bisect_left(data, (2, 'b'))  # 結果: 3
```

この二つを組み合わせて、実装できる。
初見はよくわからなかったが、ここまで整理できた段階でみると、
bisect_left自然な実装な気もして来た。

```py
class Solution:
    def search(self, nums: List[int], target: int) -> int:
        def get_priority(num: int) -> Tuple[bool, bool]:
            """最小値未満、以上で前半と後半のソート済み配列とした時、(後半に属するか、target以上か)を返す。
               具体的な使用イメージは以下。
                1. numが前半に属し、target未満  -> (False, False)
                2. numが前半に属し、target以上  -> (False, True)
                   <- targetが前半ならこれを満たす初めを得る
                3. numが後半に属し、target未満  -> (True, False)
                4. numが後半に属し、target以上  -> (True, True)
                   <- targetが後半ならこれを満たす初めを得る
            """
            return (num <= nums[-1], target <= num)

        priority_target = get_priority(target)
        i = bisect_left(nums, priority_target, key=get_priority)
        if i < len(nums) and nums[i] == target:
            return i
        return -1
```

## step3
```py
class Solution:
    def search(self, nums: List[int], target: int) -> int:
        def is_in_target_range(num: int) -> bool:
            if target > nums[-1]:  # targetが前半に存在
                if num > nums[-1]:  # numが前半に存在
                    return num >= target
                else:  # numが後半に存在
                    return True
            if target <= nums[-1]:  # targetが後半に存在
                if num > nums[-1]:  # numが前半に存在
                    return False
                else:  # numが後半に存在
                    return num >= target

        candidate_index = bisect_left(nums, True, key=is_in_target_range)
        if candidate_index < len(nums) and nums[candidate_index] == target:
            return candidate_index
        return -1
```
