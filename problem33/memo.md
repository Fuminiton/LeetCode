## 取り組み方
- step1: 5分以内に空で書いてAcceptedされるまで解く + テストケースと関連する知識を連想してみる
- step2: 他の方の記録を読んで連想すべき知識や実装を把握した上で、前提を置いた状態で最適な手法を選択し実装する
- step3: 10分以内に1回もエラーを出さずに3回連続で解く

## step1
r行c列のunique pathsを sum_paths[r][c] とすれば、

`sum[r+1][c+1] = sum[r+1][c] + sum[r][c+1]`

となるので、ボトムアップの動的計画法で解ける。

そもそも、組み合わせの問題で、下移動をm-1、右移動をn-1の組み合わせなので、m+n-2 C m-1 を出せばよい。
オーバーフローが怖いと思ったがpythonの整数型はオーバーフローを気にしなくて良いらしく、combinationのライブラリがあったので制限を見た上で使ってみる。

https://docs.python.org/3/library/stdtypes.html#numeric-types-int-float-complex
> Integers have unlimited precision.

https://docs.python.org/3/library/math.html#math.comb
> Evaluates to n! / (k! * (n - k)!) when k <= n and evaluates to zero when k > n.

結論、どちらがいいかだが、マス目上に障害物を置きたいとか追加で要件が来そうなので、動的計画法の方が柔軟で良いと思う。


#### 動的計画法
```python
class Solution:
    def uniquePaths(self, num_row: int, num_col: int) -> int:
        sum_paths = [[0] * num_col for _ in range(num_row)]
        for c in range(num_col):
            sum_paths[0][c] = 1
        for r in range(num_row):
            sum_paths[r][0] = 1
        
        for c in range(1, num_col):
            for r in range(1, num_row):
                sum_paths[r][c] = sum_paths[r - 1][c] + sum_paths[r][c - 1]
        return sum_paths[num_row - 1][num_col - 1]
```

#### 組み合わせ
```python
class Solution:
    def uniquePaths(self, m: int, n: int) -> int:
        return math.comb(m+n-2, m-1)
```

## step2
### 読んだコード
- https://github.com/hayashi-ay/leetcode/pull/39/files
- https://github.com/SuperHotDogCat/coding-interview/pull/16/files
- https://github.com/sakupan102/arai60-practice/pull/34/files

### 感想
- 1次元の配列を用いても解けるのか
  - 更新に使うのは上の値と左の値なので、1行ごとに値を更新していく流れにすれば、上の値から後ろの値を足すだけにできる
    - [1][1][1][1]
    - [1][2][3][4]
    - [1][3][6][10]
- 一方、空間計算量がm*n->min(m,n)で10^4->10^2になる嬉しさより、意図のわかりやすさを取って2次元配列で実装したいと思った

- 階乗も用意されていたのは知らなかった
  - acosh、sumprodなども用意されていた

https://docs.python.org/ja/3.13/library/math.html#math.factorial
> math.factorial(n)
> n の階乗を整数で返します。n が整数でないか、負の数の場合は、 ValueError を送出します。

- factorialの実装は分割統治で、右半分と左半分の階乗をだして左右の掛け算を最終結果とするような流れ。

https://github.com/python/cpython/blob/main/Modules/mathmodule.c#L1915


### 1DP
```python
class Solution:
    def uniquePaths(self, num_row: int, num_col: int) -> int:
        sum_paths = [1] * num_col
        for _ in range(num_row - 1):
            for c in range(1, num_col):
                sum_paths[c] += sum_paths[c-1]
        return sum_paths[num_col - 1]
```

### 2DP
```python
class Solution:
    def uniquePaths(self, height: int, width: int) -> int:
        paths = [[0] * width for _ in range(height)]
        for w in range(width):
            paths[0][w] = 1
        for h in range(height):
            paths[h][0] = 1
        for w in range(1, width):
            for h in range(1, height):
                paths[h][w] = paths[h - 1][w] + paths[h][w - 1]
        return paths[height - 1][width - 1]
```

## step3
だいぶ細かいが、0行目と0列目の初期化はiを使ってループするのも、heightとwidthに意識が行って良い気がする。

```python
class Solution:
    def uniquePaths(self, height: int, width: int) -> int:
        sum_paths = [[0] * width for _ in range(height)]
        for i in range(height):
            sum_paths[i][0] = 1
        for i in range(width):
            sum_paths[0][i] = 1
        for h in range(1, height):
            for w in range(1, width):
                sum_paths[h][w] = sum_paths[h - 1][w] + sum_paths[h][w - 1]
        return sum_paths[height - 1][width - 1]
```
