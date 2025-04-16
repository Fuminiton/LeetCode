## 取り組み方
- step1: 5分以内に空で書いてAcceptedされるまで解く + テストケースと関連する知識を連想してみる
- step2: 他の方の記録を読んで連想すべき知識や実装を把握した上で、前提を置いた状態で最適な手法を選択し実装する
- step3: 10分以内に1回もエラーを出さずに3回連続で解く

## step1
nums[i]からnums[j]までの和を全てみて一番大きいものを返せば良さそう。
累積和を用いれば、i~jまでの和がO(1)でアクセスできるので、全体としてはO(nums.length^2)で解ける。
が、10^5*10^5で10^10となるので、計算に数時間くらいかかってしまいそう。

ので、効率化できないかを考える。
求めたいのは、prefix_sum[j+1]-prefix_sum[i]をi,j(i<=j)を動かしていった際の最大の値。
jを固定して考えると、prefix_sum[i]が最小の時がprefix_sum[j+1]-prefix_sum[i]が最大となる。
したがって、jを0~len(nums)-1まで動かしていって、
その際の累積和とこれまでの累積和の最小値の差をmaximum subarrayとして更新していけば、O(nums.length)で解ける。

```python
class Solution:
    def maxSubArray(self, nums: List[int]) -> int:
        maximum_subarray = -sys.maxsize - 1
        sum_prefix = 0
        minimum_sum_prefix = 0
        for num in nums:
            sum_prefix += num
            maximum_subarray = max(
                maximum_subarray,
                sum_prefix - minimum_sum_prefix
            )
            minimum_sum_prefix = min(
                minimum_sum_prefix,
                sum_prefix
            )
        return maximum_subarray
```

## step2
### 読んだコード
- https://github.com/TORUS0818/leetcode/pull/34/files
- https://github.com/hayashi-ay/leetcode/pull/36/files
- https://github.com/sakupan102/arai60-practice/pull/33/files

### 感想
- subarrayの要素の和の最大を出したいので、変数名が不適切だった。なぜ気づかなかった、
- wikipediaに取り上げられるくらい有名な問題で、別解も色々あった
  - https://en.wikipedia.org/wiki/Maximum_subarray_problem
    - kadaneのアルゴリズム
    - 分割統治
    - 総当たり(累積和なし)
    - 総当たり(累積和あり) <- 初見時、提出してTLEになった
- 分割統治は、並列処理が適切な場面では選択肢になるのだろうか
- kadaneは変数が減らせるくらいが嬉しさだろうと思うので、step1の実装の方がわかりやすくいい気がする

##### kadaneのアルゴリズム
step1の実装の`maximum_subarray`の更新部分`sum_prefix - minimum_sum_prefix`を式変形していくと、
`-> sum_prefix - min(prev_minimum_sum_prefix, prev_sum_prefix)`
`-> num + prev_sum_prefix - min(prev_minimum_sum_prefix, prev_sum_prefix)`
`-min(A, B) = max(-A, -B)`であることから、
`-> num + max(prev_sum_prefix - prev_minimum_sum_prefix, prev_sum_prefix - prev_sum_prefix)`
`-> num + max(prev_sum_prefix - prev_minimum_sum_prefix, 0)`
`num`を共通因子としてmaxの中に入れて、
`-> max(num, num + prev_sum_prefix - prev_minimum_sum_prefix)`
ここで、`prev_sum_prefix - prev_minimum_sum_prefix`は、
`sum_prefix - minimum_sum_prefix`の1つ前の結果だったということになるので、
`sum_prefix - minimum_sum_prefix`を`current_max_sum`とでもおいて、
`current_max_sum = max(num, num + current_max_sum)`を更新していけば良いという話。

```python
class Solution:
    def maxSubArray(self, nums: List[int]) -> int:
        max_sum = -sys.maxsize - 1
        current_max_sum = -sys.maxsize - 1
        for num in nums:
            current_max_sum = max(num, num + current_max_sum)
            max_sum = max(max_sum, current_max_sum)
        return max_sum
```

##### 分割統治
配列を半分にして、1.左半分と2.右半分, 3.分割する場所を跨ぐ部分配列の中で和が最大になるものを探すという発想。
1.2.は再帰的に出せば良い。
3.は真ん中から左端に向かって最大値を記録していく + 真ん中+1から右端に向かって最大値を記録していき、2つの和を出す。

再帰の深さは`logN`で3.の処理が`N`なので、O(NlogN)で解ける。


```python
class Solution:
    def maxSubArray(self, nums: List[int]) -> int:
        def get_max_sum(left: int, right: int) -> int:
            if left == right:
                return nums[left]
            center = (left + right) // 2
            left_max_sum = get_max_sum(left, center)
            right_max_sum = get_max_sum(center + 1, right) 
            center_max_sum = get_center_max_sum(left, right)
            return max(
                left_max_sum,
                right_max_sum,
                center_max_sum
            )

        def get_center_max_sum(left: int, right: int) -> int:
            if left == right:
                return nums[left]
            center = (left + right) // 2
            
            left_max_sum = -sys.maxsize
            left_direction_prefix_sum = 0
            for i in range(center, left - 1, -1):
                left_direction_prefix_sum += nums[i]
                left_max_sum = max(
                    left_max_sum,
                    left_direction_prefix_sum
                )
            right_max_sum = -sys.maxsize
            right_direction_prefix_sum = 0
            for i in range(center + 1, right + 1):
                right_direction_prefix_sum += nums[i]
                right_max_sum = max(
                    right_max_sum,
                    right_direction_prefix_sum
                )
            return left_max_sum + right_max_sum

        return get_max_sum(0, len(nums) - 1)
```

## step3
分割統治も応用が効きそうなので、3回解いて定着させておく。

```python
class Solution:
    def maxSubArray(self, nums: List[int]) -> int:
        max_subarray_sum = -sys.maxsize - 1
        prefix_sum = 0
        min_prefix_sum = 0
        for num in nums:
            prefix_sum += num
            max_subarray_sum = max(
                max_subarray_sum,
                prefix_sum - min_prefix_sum
            )
            min_prefix_sum = min(
                min_prefix_sum,
                prefix_sum
            )
        return max_subarray_sum
```

```python
class Solution:
    def maxSubArray(self, nums: List[int]) -> int:
        def get_max_sum(left: int, right: int) -> int:
            if left == right - 1:
                return nums[left]
            center = (left + right) // 2
            left_max_sum = get_max_sum(left, center)
            right_max_sum = get_max_sum(center, right)
            center_max_sum = get_max_center_sum(left, right)
            return max(left_max_sum, center_max_sum, right_max_sum)
        
        def get_max_center_sum(left: int, right: int) -> int:
            center = (left + right) // 2
            left_max_sum = right_max_sum = -sys.maxsize - 1
            left_prefix_sum = right_prefix_sum = 0
            for i in range(center - 1, left - 1, -1):
                left_prefix_sum += nums[i]
                left_max_sum = max(left_max_sum, left_prefix_sum)
            for i in range(center, right):
                right_prefix_sum += nums[i]
                right_max_sum = max(right_max_sum, right_prefix_sum)
            return left_max_sum + right_max_sum

        return get_max_sum(0, len(nums))
```