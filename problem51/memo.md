## 取り組み方
- step1: 5分以内に空で書いてAcceptedされるまで解く + テストケースと関連する知識を連想してみる
- step2: コードを整える + 他の妥当な実装があれば実装してみる
- step3: 10分以内に1回もエラーを出さずに3回連続で解く

## step1
subarrayの先頭window_startと末尾window_end、subarrayに含まれるnumの和window_sumを管理しながら、和がtarget以上になる最小の長さを探す。

具体的には、window_endを1ずつ進めていくループの中で、window_sumがtarget以上になったらtarget未満になるまでwindow_startを1ずつ進めるような操作と最小の長さを更新するのを繰り返す。

時間計算量はO(nums.length)なので数百m秒程度で処理が完了する見込み。

```py
class Solution:
    def minSubArrayLen(self, target: int, nums: List[int]) -> int:
        window_sum = 0
        window_start = 0
        min_len = inf

        for window_end, num in enumerate(nums):
            window_sum += num
            while window_sum >= target:
                min_len = min(min_len, window_end - window_start + 1)
                window_sum -= nums[window_start]
                window_start += 1

        if isinf(min_len):
            return 0
        return min_len
```

## step2
### 読んだ
https://github.com/fhiyo/leetcode/pull/49/files
https://github.com/shining-ai/leetcode/pull/49/files
https://github.com/hroc135/leetcode/pull/46/files

### 感想
- infがfloat型なのが微妙(と思いながら書いてしまった)
  - 以下の解決策がよいと思った
> 一つ考えたのは、target <= sum(nums) を確認すれば、min_length = len(nums) がいえますね。
https://github.com/shining-ai/leetcode/pull/49/files#r1563843030

- 以下を読んでいて、そもそもこの関数の用途はどんなものかを考える工程が40問目をやっていた時期より甘くなっていた気がするので反省

> 計算量はあくまでも極限を取ったときの振る舞いなので、実際に大事なのは、用途から考えられる状況において「何秒」で実行できるかの見積もりです。「用途(目的)」と「何秒(価値)」です。
https://github.com/Hurukawa2121/leetcode/pull/1#discussion_r1869920625

- 「連続した要素の合計が、ある条件を満たす最小の区間を見つける」というシナリオなので、
  - ユースケースは
    - 在庫管理やデータ分析あたりだろうか
    - 連続したメモリブロックの回収とかでも使われてそう
  - 今後を見据えてケアしておかないといけないのは
    - numsに負の数が含まれる
      - これは今回のアルゴリズムだと厳しそう
    - numsに浮動小数点が含まれる

```py
class Solution:
    def minSubArrayLen(self, target: int, nums: List[int]) -> int:
        if sum(nums) < target:
            return 0
        
        min_len = sum(nums)
        window_sum = 0
        window_start = 0

        for window_end, num in enumerate(nums):
            window_sum += num
            while window_sum >= target:
                min_len = min(min_len, window_end - window_start + 1)
                window_sum -= nums[window_start]
                window_start += 1

        return min_len
```

## step3
```py
class Solution:
    def minSubArrayLen(self, target: int, nums: List[int]) -> int:
        if sum(nums) < target:
            return 0
        
        min_len = sum(nums)
        window_sum = 0
        start = 0
        
        for end, num in enumerate(nums):
            window_sum += num
            while window_sum >= target:
                min_len = min(min_len, end - start + 1)
                window_sum -= nums[start]
                start += 1
        
        return min_len
```