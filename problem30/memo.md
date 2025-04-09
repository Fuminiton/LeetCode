## 取り組み方
- step1: 5分以内に空で書いてAcceptedされるまで解く + テストケースと関連する知識を連想してみる
- step2: 他の方の記録を読んで連想すべき知識や実装を把握した上で、前提を置いた状態で最適な手法を選択し実装する
- step3: 10分以内に1回もエラーを出さずに3回連続で解く
- step4: LRU cacheを実装した上で解く

## step1
### 考えたこと
前と同じ色であることをo、違う色であることをxとしたときに、連続する3つの(i-2, i-1, i)番のフェンスについて考える。
i番目がi-1番目と同じ色で塗りたい時のパターンは

- o - o : これはルール違反
- x - o : i番目にi-1番目と同じ色を使う

i番目がi-1番目と違う色で塗りたい時のパターンは

- o - x : i番目にk-1色使える 
- x - x : i番目にk-1色使える

と整理できるので、動的計画法を用いて求められる。
時間計算量は1~n番目まで順番に更新していくので、O(n)。空間計算量も1~n番目用に箱を作るのでO(n)。

動的計画法用の配列名って`dp`で問題ないのだろうか。一旦、`ways_is_same_before`あたりにしておく。
トップダウン型でも解く。

#### ボトムアップ
```python
class Solution:
    def numWays(self, n: int, k: int) -> int:
        if n == 1:
            return k
        ways_is_same_before = [{True: 0, False: 0} for _ in range(n)]
        ways_is_same_before[1][True] = k
        ways_is_same_before[1][False] = k * (k - 1)

        def get_total_ways(i: int) -> int:
            return ways_is_same_before[i][True] + ways_is_same_before[i][False]

        for i in range(2, n):
            ways_is_same_before[i][True] = ways_is_same_before[i - 1][False]
            ways_is_same_before[i][False] = get_total_ways(i - 1) * (k - 1)
        
        return get_total_ways(n - 1)
```

#### トップダウン
途中で気づいたが、

- 前のフェンスと違う色を選ぶ場合
  - 前の柱の塗り方のパターン * (k - 1)
- 前のフェンスと同じ色を選ぶ場合
  - 3本連続で同じ色にならない制約から、前々のフェンスと違う色を選ぶ必要があり
  - 前々のフェンスの塗り方のパターン * (k - 1)

でも解ける。こちらの方が1次元配列で良く、変数名も短くなって可読性も高そう。

```python
class Solution:
    def numWays(self, n: int, k: int) -> int:
        index_to_ways = {}

        def num_ways_helper(i: int) -> int:
            if i == 0:
                return k
            if i == 1:
                return k * k
            if i in index_to_ways:
                return index_to_ways[i]
            index_to_ways[i] = (k - 1) * (num_ways_helper(i - 2) + num_ways_helper(i - 1))
            return index_to_ways[i]

        return num_ways_helper(n - 1)
```

## step2
### 読んだコード
- https://github.com/hayashi-ay/leetcode/pull/17/files
- https://github.com/TORUS0818/leetcode/pull/32/files
- 

### 感想
- 0-indexで考えていたが、1-indexにしても分かりやすくて良さそう
- トップダウンで`@cache`を使う実装が読みやすく、編集もしやすそうな気がする
- 配列にして管理せず、2つ前と1つ前の状態をとっておく方法もあった
- 「nが1の時」といったように`n`を条件につけると、`0-index`としてインデックスを使うときに混同しそうで避けたいかも
- 再帰にしなくてもシンプルに書けそうなので、ボトムアップ型で2つ前、1つ前の状態を保持しながら進んでいく実装方針を選ぶ

```python
class Solution:
    def numWays(self, n: int, k: int) -> int:
        if n == 1:
            return k
        if n == 2:
            return k * k
        prev_prev_num_ways = k
        prev_num_ways = k * k
        num_ways = 0
        loop_count = 1
        while 2 + loop_count <= n:
            num_ways = (k - 1) * (prev_prev_num_ways + prev_num_ways)
            prev_prev_num_ways = prev_num_ways
            prev_num_ways = num_ways
            loop_count += 1
        return num_ways
```

## step3
#### ボトムアップ

```python
class Solution:
    def numWays(self, n: int, k: int) -> int:
        """Using 1-index"""
        if n == 1:
            return k
        prev_prev_ways = k
        prev_ways = k * k
        ways = k * k
        for _ in range(2, n):
            ways = (k - 1) * prev_prev_ways + (k - 1) * prev_ways
            prev_prev_ways, prev_ways = prev_ways, ways
        return ways
```

#### トップダウン

```python
class Solution:
    def numWays(self, n: int, k: int) -> int:
        """Using 1-index"""
        @cache
        def get_ways(index: int):
            if index == 1:
                return k
            if index == 2:
                return k * k
            ways = (k - 1) * get_ways(index - 2)  
            ways += (k - 1) * get_ways(index - 1)
            return ways

        return get_ways(n)
```

## step4
LRU cacheを実装する。
そもそも、LRU cacheとは、格納できる領域が限られる場合に使えるキャッシュ。

結局、実現したいのは、

1. 関数呼び出しの引数と結果を辞書に保存する
2. 最古の1.が先頭、最新が末尾にくるようなデータ構造を用意する
3. 関数が呼び出されたとき、辞書にアクセスして保存してあれば返す
4. 関数の呼び出しの引数と結果を辞書に保存する
   - すでに辞書に保存されている場合は削除
   - また、辞書が制限サイズを超えていれば最古のデータを削除
   - 辞書に新たに引数と結果を登録する
5. 関数の呼び出しの引数と結果を2.のデータ構造に追加する
   - すでに登録されていれば削除
   - 制限サイズを超えていれば、先頭のデータを削除
   - データを末尾に追加

ということ。
データ構造は、Doubly-Linked List or OrderedDict を使う方針がありそう。
前者の方がカスタマイズ性などが優れていそうだが、後者の方がシンプルに書けそうなので、今回は後者で書く。
デコレータにするのは一旦、保留する。

```python
from collections import OrderedDict

class MyLRUCache:
    def __init__(self, capacity):
        self.cache = OrderedDict()
        self.capacity = capacity
    
    def get(self, key) -> int | None:
        if not key in self.cache:
            return None
        value = self.cache.pop(key)
        self.cahce[key] = value
        return value

    def put(self, key, value) -> None:
        if key in self.cache:
            self.cache.pop(key)
        if not len(self.cache) < self.capacity:
            self.cache.popitem(last=False)
        self.cache[key] = value


class Solution:
    def numWays(self, n: int, k: int) -> int:
        """Using 1-index"""
        cache = MyLRUCache(capacity=1000)

        def get_ways(index: int):
            if index == 1:
                return k
            if index == 2:
                return k * k
            ways = cache.get(n)
            if ways is not None:
                return ways
            ways = (k - 1) * get_ways(index - 2) + (k - 1) * get_ways(index - 1)
            cache.put(index, ways)
            return ways

        return get_ways(n)
```

### 感想
- 時間をかけすぎた感があるのでやらなかったが、後日、doubly-linked listを使った実装とcpythonの実装箇所の読み込みはやる
- 公式ドキュメントのOrderedDictの使用例に、lru_cacheの亜種の実装があって興味深かった

https://docs.python.org/3.13/library/collections.html#collections.OrderedDict
> class LastUpdatedOrderedDict(OrderedDict):
> class TimeBoundedLRU:
> class MultiHitLRUCache:
