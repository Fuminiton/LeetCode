## 取り組み方
- step1: 5分以内に空で書いてAcceptedされるまで解く + テストケースと関連する知識を連想してみる
- step2: 他の方の記録を読んで連想すべき知識や実装を把握した上で、前提を置いた状態で最適な手法を選択し実装する
- step3: 10分以内に1回もエラーを出さずに3回連続で解く

## step1
min_steps[x] を値 x を作るために必要な最小手数とすると、x == coin なら手数1で作れる。
そうでない時、nums の中の num の候補の中で、x-num が作れる場合は min_steps[x-num]+1 が最小の時が最小の手数。

amount、numsが空の時、作れない時は-1を返す。

amount * len(coins) が時間計算量だが、10^4 * 12 ~ 10^5程度なので、現状は問題ない。
実際、このプログラムが使われるのは自動販売機のようなお釣りを出すシステムだと思うので、amountが10^6であったり、coinの種類が1000個になったりすることは考えにくいので、一旦、この実装で良いと思う。

ちょっと、分かりにくいコードになってしまった気もするが、step2で整える。

```python
class Solution:
    def coinChange(self, coins: List[int], amount: int) -> int:
        if not coins:
            return -1
        if amount is None:
            return -1
        
        NOT_FOUND = -1
        min_steps = [NOT_FOUND] * (amount + 1)
        min_steps[0] = 0

        for x in range(1, amount + 1):
            for coin in coins:
                if x - coin < 0:
                    continue
                if min_steps[x - coin] == NOT_FOUND:
                    continue
                if min_steps[x] == NOT_FOUND:
                    min_steps[x] = min_steps[x - coin] + 1
                    continue
                min_steps[x] = min(
                    min_steps[x], 
                    min_steps[x - coin] + 1
                )
        return min_steps[amount]
```

## step2
### 読んだコード
- https://github.com/shining-ai/leetcode/pull/40/files
- https://github.com/fhiyo/leetcode/pull/41/files
- https://github.com/hayashi-ay/leetcode/pull/68/files

### 感想
- 値を作れなかった場合に`-1`を返す要件があるので、配列を`math.inf`ではなく`-1`で初期化したが、minを取るので大きい数の方が簡潔なコードになるので悩ましい
- x - coin は remaining 等の変数に置くのが親切
- その他の実装方針として、
  - 最小手数を求めるので最短経路問題としても扱える
  - 今回はトップダウン型のDPを使うのも素直だと思う
  - 3つの選択肢のうち、どれを選んでも、時間計算量がamount*len(coins)、空間計算量がamountになる
  - DPであれば、1度計算しておけば再利用できるので、自動販売機システムを想定するとDPが適切なように思う
- 必要なコインの枚数の最小を求めたいので、needed_num_coinsあたりの方が適切か

#### step1の改良: 変数名とinfによる初期化
```python
class Solution:
    def coinChange(self, coins: List[int], amount: int) -> int:
        if not coins:
            return -1
        if amount is None:
            return -1

        needed_num_coins = [inf] * (amount + 1)
        needed_num_coins[0] = 0

        for target in range(1, amount + 1):
            for coin in coins:
                remaining = target - coin
                if remaining < 0:
                    continue
                needed_num_coins[target] = min(
                    needed_num_coins[target],
                    needed_num_coins[remaining] + 1
                )
        if isinf(needed_num_coins[amount]):
            return -1
        return needed_num_coins[amount]
```

#### topdown dp: remaining - coin を探す
```python
class Solution:
    def coinChange(self, coins: List[int], amount: int) -> int:
        if not coins:
            return -1
        if amount is None:
            return -1

        @lru_cache
        def find_min_needed_num_coins(target: int) -> int:
            if target < 0:
                return inf
            if target == 0:
                return 0
            min_needed = inf
            for coin in coins:
                remaining = target - coin
                min_needed = min(
                    min_needed,
                    1 + find_min_needed_num_coins(remaining)
                )
            return min_needed

        min_needed = find_min_needed_num_coins(amount)
        if isinf(min_needed):
            min_needed = -1
        return min_needed
```

#### bfs: coinで作れる合計金額をなるたけ作ってamountと一致するor amountを超えないパターンを前列挙するまで続ける
```python
class Solution:
    def coinChange(self, coins: List[int], amount: int) -> int:
        if not coins:
            return -1
        if amount is None:
            return -1
        if amount == 0:
            return 0

        money_sums = [0]
        needed_steps = 1
        visited = set([0])

        while money_sums:
            next_money_sums = []
            for money_sum in money_sums:
                for coin in coins:
                    new_money_sum = money_sum + coin
                    if new_money_sum == amount:
                        return needed_steps
                    if new_money_sum > amount:
                        continue
                    if new_money_sum in visited:
                        continue
                    next_money_sums.append(new_money_sum)
                    visited.add(new_money_sum)
            needed_steps += 1
            money_sums = next_money_sums
        return -1  # Not found
 ```

## step3
```python
class Solution:
    def coinChange(self, coins: List[int], amount: int) -> int:
        if not coins:
            return -1
        if amount is None:
            return -1
        needed_num_coins = [inf] * (amount + 1)
        needed_num_coins[0] = 0
        
        for current_amount in range(1, amount + 1):
            for coin in coins:
                remaining_amount = current_amount - coin
                if remaining_amount < 0:
                    continue
                needed_num_coins[current_amount] = min(
                    needed_num_coins[current_amount],
                    needed_num_coins[remaining_amount] + 1
                )
        if isinf(needed_num_coins[amount]):
            return -1
        return needed_num_coins[amount]
```
