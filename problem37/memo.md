## 取り組み方
- step1: 5分以内に空で書いてAcceptedされるまで解く + テストケースと関連する知識を連想してみる
- step2: 他の方の記録を読んで連想すべき知識や実装を把握した上で、前提を置いた状態で最適な手法を選択し実装する
- step3: 10分以内に1回もエラーを出さずに3回連続で解く

## step1
「今の値 - これまでの最小値」を2番目の値から最大利益として更新していく方針で解ける。
利益を達成できないシチュエーションは、pricesの変動が1回も起きない時と最大利益が負になっている時。

```python
class Solution:
    def maxProfit(self, prices: List[int]) -> int:
        if prices is None:
            return 0
        if len(prices) < 2:
            return 0
        max_profit = 0
        min_price_so_far = prices[0]
        for price in prices[1:]:
            max_profit = max(
                max_profit,
                price - min_price_so_far
            )
            min_price_so_far = min(
                min_price_so_far,
                price
            )
        return max(max_profit, 0)
```

## step2
### 読んだコード
- https://github.com/fhiyo/leetcode/pull/38/files
- https://github.com/hayashi-ay/leetcode/pull/52/files
- https://github.com/TORUS0818/leetcode/pull/39/files

### 感想
- max_profitを0から始めているので、return分のmax(x, 0)は余計だった
- pricesをひっくり返したループで、これまでの最大を保持して更新していく方針もある
  - 時系列で最大利益をウォッチして行きたい気がするので、普通に前から見ていった方が良いのでは
- どうでもいいことだが、最低価格ならminではなく、lowestか
- for文のindexで回すかスライスで回すかだが、巨大な配列今回は読みやすさを取った
  - 1日の価格程度しか見ていないので、100年生きても高々365*10^2~ 10^4くらいのオーダーなので十分小さい

```python
class Solution:
    def maxProfit(self, prices: List[int]) -> int:
        if not prices:
            return 0
        if len(prices) < 2:
            return 0
        max_profit = 0
        lowest_price_so_far = prices[0]
        for price in prices[1:]:
            potential_profit = price - lowest_price_so_far
            max_profit = max(max_profit, potential_profit)
            lowest_price_so_far = min(lowest_price_so_far, price)
        return max_profit
```

## step3
```python
class Solution:
    def maxProfit(self, prices: List[int]) -> int:
        if not prices:
            return 0
        if len(prices) < 2:
            return 0
        max_profit = 0
        lowest_profit_so_far = prices[0]
        for price in prices[1:]:
            max_profit = max(
                max_profit,
                price - lowest_profit_so_far
            )
            lowest_profit_so_far = min(
                lowest_profit_so_far,
                price
            )
        return max_profit
```