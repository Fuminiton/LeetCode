## 取り組み方
- step1: 5分以内に空で書いてAcceptedされるまで解く + テストケースと関連する知識を連想してみる
- step2: 他の方の記録を読んで連想すべき知識や実装を把握した上で、前提を置いた状態で最適な手法を選択し実装する
- step3: 10分以内に1回もエラーを出さずに3回連続で解く

## step1
価格が下がったら売って、新しい買い入れポイントを設定していく流れで解けるはず。
と思ったが、単調増加なら最終的に「売ったとき - 初めに買ったとき」になるので、
前日と当日の差額を考えて、プラスなら足して、そうでなければスルーするだけで良い。
初めと終わりの処理で不足がないように気を付ける。

```python
class Solution:
    def maxProfit(self, prices: List[int]) -> int:
        if not prices:
            return 0
        if len(prices) == 1:
            return 0
        profit = 0
        prev_price = prices[0]
        for price in prices[1:]:
            if price - prev_price > 0:
                profit += price - prev_price
            prev_price = price
        return profit
```

## step2
### 読んだコード
- https://github.com/shining-ai/leetcode/pull/38/files
- https://github.com/hayashi-ay/leetcode/pull/56/files
- https://github.com/fuga-98/arai60/pull/38

### 感想
- 単調増加してい/していで分けて考えていく -> 単調増加なら日々売り買いしていけばいい -> step1のコードのようになるので、若干パズルをさせてしまうかもしれない
- prevよりyesterdayの方が親切

#### step1の改良
```python
class Solution:
    def maxProfit(self, prices: List[int]) -> int:
        if not prices:
            return 0
        if len(prices) == 1:
            return 0
        profit = 0
        yesterday_price = prices[0]
        for price in prices[1:]:
            if price - yesterday_price > 0:
                profit += price - yesterday_price
            yesterday_price = price
        return profit
```

#### 単調増加でなくなったタイミングで利益を確定させる
- 最後にまだ売っていない株があれば売る

```python
class Solution:
    def maxProfit(self, prices: List[int]) -> int:
        if not prices:
            return 0
        if len(prices) == 1:
            return 0
        profit = 0
        buy_price = prices[0]
        for i in range(1, len(prices)):
            if prices[i] >= prices[i - 1]:
                continue
            profit += max(0, prices[i - 1] - buy_price)
            buy_price = prices[i]
        profit += max(0, prices[-1] - buy_price)
        return profit
```

## step3
```python
class Solution:
    def maxProfit(self, prices: List[int]) -> int:
        if not prices:
            return 0
        if len(prices) == 1:
            return 0
        profit = 0
        yesterday_price = prices[0]
        for price in prices[1:]:
            profit += max(0, price - yesterday_price)
            yesterday_price = price
        return profit
```