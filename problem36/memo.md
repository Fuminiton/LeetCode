## 取り組み方
- step1: 5分以内に空で書いてAcceptedされるまで解く + テストケースと関連する知識を連想してみる
- step2: 他の方の記録を読んで連想すべき知識や実装を把握した上で、前提を置いた状態で最適な手法を選択し実装する
- step3: 10分以内に1回もエラーを出さずに3回連続で解く

## step1
これまでの最大値は、1つ前までの最大値 vs 2つ前までの最大値+nums[i]の大きい方を求める。
この時、0番目の家から盗むか盗まないかで場合分けが必要で、

- 盗む場合
  - len(nums) - 1は盗めない
- 盗まない場合
  - len(nums) - 1を盗める

となる。
計算量はO(nums.length)。
弾いておきたいケースは、numsが空の場合。

実装としては、startとgoalを渡して、最大利益を探す関数を2回呼び出す方針が綺麗そう。

```python
class Solution:
    def rob(self, nums: List[int]) -> int:
        if nums is None:
            return -1
        if len(nums) <= 2:
            return max(nums)

        max_profit = 0

        def rob_from_given_range(start: int, goal: int) -> int:
            max_profit_so_far = 0
            max_profit_1_ago = 0
            max_profit_2_ago = 0
            for i in range(start, goal):
                max_profit_so_far = max(
                    max_profit_1_ago,
                    max_profit_2_ago + nums[i]
                )
                max_profit_2_ago = max_profit_1_ago
                max_profit_1_ago = max_profit_so_far
            return max_profit_so_far

        max_profit = max(
            max_profit,
            rob_from_given_range(0, len(nums) - 1)
        )
        max_profit = max(
            max_profit,
            rob_from_given_range(1, len(nums))
        )
        return max_profit
```

## step2
### 読んだコード
- https://github.com/hayashi-ay/leetcode/pull/50
- https://github.com/fhiyo/leetcode/pull/37/files
- https://github.com/seal-azarashi/leetcode/pull/34/files


### 感想
- 先頭を使うケース、使わないケース用に別々の変数セットを用意して、1周のループで解いてしまう解法もあった
  - この解き方も思い浮かんだ上で、泥棒によって盗める範囲が変わるような変更への柔軟さ等を加味して、step1の実装を選びたかった。。
- 範囲を与えるより、配列を渡した方が応用範囲が広く、かつコードも単純になって良さそう
- 先頭に入るケース、入らないケースというコメントを残している方がいて、確かに書いた方が読み手にパズルをさせないように思う
- helper関数の命名についても何をやっているかがわかる命名が良さそう

```python
class Solution:
    def rob(self, nums: List[int]) -> int:
        if nums is None:
            return -1
        if len(nums) == 1:
            return nums[0]

        def get_max_profit_from_nums(nums: List[int]) -> int:
            max_profit_so_far = 0
            max_profit_1_ago = 0
            max_profit_2_ago = 0
            for num in nums:
                max_profit_so_far = max(
                    max_profit_1_ago,
                    max_profit_2_ago + num
                )
                max_profit_2_ago = max_profit_1_ago
                max_profit_1_ago = max_profit_so_far
            return max_profit_so_far

        return max(
                get_max_profit_from_nums(nums[:len(nums)-1]),
                get_max_profit_from_nums(nums[1:])
            )
```

## step3
```python
class Solution:
    def rob(self, nums: List[int]) -> int:
        if not nums:
            return 0
        if len(nums) < 2:
            return nums[0]
        
        def get_max_profit_from_nums(nums: List[int]) -> int:
            max_profit = 0
            max_profit_1_ago = 0
            max_profit_2_ago = 0
            for num in nums:
                max_profit = max(
                    max_profit_2_ago + num,
                    max_profit_1_ago
                )
                max_profit_2_ago = max_profit_1_ago
                max_profit_1_ago = max_profit
            return max_profit
        
        return max(
            get_max_profit_from_nums(nums[:-1]),
            get_max_profit_from_nums(nums[1:]))
```
