## 取り組み方
- step1: 5分以内に空で書いてAcceptedされるまで解く + テストケースと関連する知識を連想してみる
- step2: 他の方の記録を読んで連想すべき知識や実装を把握した上で、前提を置いた状態で最適な手法を選択し実装する
- step3: 10分以内に1回もエラーを出さずに3回連続で解く

## step1
`nums`の要素がi番目までの時、
盗める最大はひとつ前を使うか、使わないで`nums[i]`を使うかの大きい方が答え。
つまり、`amount_total_money`、`prev_amount_total_money`、`prev_prev_amount_total_money`を管理していけば良い。

```python
class Solution:
    def rob(self, nums: List[int]) -> int:
        if len(nums) == 0:
            return 0
        if len(nums) == 1:
            return nums[0]
        prev_prev_total_amount_money = nums[0]
        prev_total_amount_money = max(nums[0], nums[1])
        total_amount_money = max(
            prev_prev_total_amount_money,
            prev_total_amount_money
        )
        for i in range(2, len(nums)):
            total_amount_money = max(
                prev_prev_total_amount_money + nums[i],
                prev_total_amount_money
            )
            prev_prev_total_amount_money = prev_total_amount_money
            prev_total_amount_money = total_amount_money
        return total_amount_money
```

## step2
### 読んだコード
- https://github.com/hayashi-ay/leetcode/pull/48/files
- https://github.com/fhiyo/leetcode/pull/36/files
- https://github.com/nittoco/leetcode/pull/39/files

### 感想
- 変数名が微妙、`max_money_so_far`あたりをつけるべき。
- 更新のループは0からはじめるようにしても良いが、2から始めた方が意図が伝わる気がする。
- メモ化再帰の発想がパッとは思い付かない。規則性がみえたら漸化式をおいてみる->ボトムアップのアプローチと考えがちな気がする。

```python
class Solution:
    def rob(self, nums: List[int]) -> int:
        if len(nums) <= 2:
            return max(nums)
        prev_prev_max_money = nums[0]
        prev_max_money = max(nums[0], nums[1])
        max_money = None

        for num in nums[2:]:
            max_money = max(
                num + prev_prev_max_money,
                prev_max_money
            )
            prev_prev_max_money = prev_max_money
            prev_max_money = max_money
        return max_money
```

## step3
1,2回目はstep2とほぼ同じになってしまったので、3回目は0番目からループに入れるようにした。悪くない気もする。

```python
class Solution:
    def rob(self, nums: List[int]) -> int:
        SENTINEL = 0
        prev_prev_max_money = SENTINEL
        prev_max_money = SENTINEL
        max_money = SENTINEL

        for num in nums:
            max_money = max(
                num + prev_prev_max_money,
                prev_max_money
            )
            prev_prev_max_money = prev_max_money
            prev_max_money = max_money
        return max_money
```
