## 取り組み方
- step1: 5分以内に空で書いてAcceptedされるまで解く
- step2: discord内で他の方のコードを5人分読む + 何をよい/悪いと思ったか、何を避けたいと思ったか等感じたことを3つ以上かく
- step3: 10分以内に1回もエラーを出さずに3回連続で解く

## step1
### 考えたこと
subarray の候補は、先頭と末尾の選び方の組み合わせ分できるので、全探索するのは 2*10^4*2*10^4~10^9 で近く、厳しそう。

subarray が k になるのは、0 <= X <= Y < len(nums) の時、「Y までの累積和 - X までの累積和」が k になること。
すなわち、「k + X までの累積和 = Y までの累積和」を考えれば良いから、
Y を 0~len(nums)-1 でループして、それぞれの時点の「あるところまでの累積和」と出現回数の hashmap を更新して、
現在までの累積和 - k をキーにこの hashmap のバリューを答えとしてカウントしていけば良い。
ただし、該当の部分配列が先頭から始まるケースもあるため、hashmap は0がキーの時、1で初期化する。

時間計算量は、Y を動かして hashmap にアクセスするので、O(len(nums)* 1) で 2*10^4 で問題なさそう。

テストを考えるなら、numsが空、kが0、numsが正負混在のパターンくらいか。


```python
class Solution:
    def subarraySum(self, nums: List[int], k: int) -> int:
        accumulate_to_frequency = defaultdict(int)
        accumulate_to_frequency[0] += 1
        accumulate = 0
        sum_equals_k = 0

        for num in nums:
            accumulate += num
            sum_equals_k += accumulate_to_frequency[accumulate - k]
            accumulate_to_frequency[accumulate] += 1

        return sum_equals_k
```

## step2
### 読んだコード
- https://github.com/Mike0121/LeetCode/pull/33/files
- https://github.com/nittoco/leetcode/pull/22/files
- https://github.com/goto-untrapped/Arai60/pull/28/files
- https://github.com/fhiyo/leetcode/pull/19/files
- https://github.com/Hurukawa2121/leetcode/pull/16/files
- https://github.com/quinn-sasha/leetcode/pull/16/files

### 読んだ感想
- accumulate より prefix_sum の方が一般的で適切なように見えた。
- ちょっとパズルチックなのでコメントがついていた方が親切かなと思った。
- defaultdictを使わない実装や別言語の実装をみて、defaultdictの初期化は便利すぎて、キーが存在していない時の処理を忘れてしまはないか怖いなあと感じた。

```python
class Solution:
    def subarraySum(self, nums: List[int], k: int) -> int:
        prefix_sum_to_frequency = defaultdict(int)
        prefix_sum_to_frequency[0] += 1
        prefix_sum = 0
        sum_equals_k = 0

        for num in nums:
            prefix_sum += num
            sum_equals_k += prefix_sum_to_frequency[prefix_sum - k]
            prefix_sum_to_frequency[prefix_sum] += 1
        return sum_equals_k
```

## step3
```python
class Solution:
    def subarraySum(self, nums: List[int], k: int) -> int:
        prefix_sum_to_frequency = defaultdict(int)
        prefix_sum_to_frequency[0] = 1
        prefix_sum = 0
        subarray_sum_equals_k = 0

        for num in nums:
            prefix_sum += num
            subarray_sum_equals_k += prefix_sum_to_frequency[prefix_sum - k]
            prefix_sum_to_frequency[prefix_sum] += 1
        return subarray_sum_equals_k
```