## 取り組み方
- step1: 5分以内に空で書いてAcceptedされるまで解く + テストケースと関連する知識を連想してみる
- step2: 他の方の記録を読んで連想すべき知識や実装を把握した上で、前提を置いた状態で最適な手法を選択し実装する
- step3: 10分以内に1回もエラーを出さずに3回連続で解く

## step1
### 考えたこと
ある節点と節点の値がとり得る範囲を受け取り、節点がとり得る値の範囲でなければFalseを返すという探索を再帰的に行う。
再帰の終了条件は、節点がNoneの時、Trueを返す。
計算量は木全体を探索するので、木の接点の数をNとして、時間も空間がO(N)。

テストケースとして考えるべきなのは、

- 空
- rootのみ
- 普通のBST
- 右の子が親より小さい
- 左の子が親より大きい
- 左部分木の右端が右部分木の左端より大きい
- 2**31の値が含まれる
- -2**31の値が含まれる

くらいか。

```python
class Solution:
    def isValidBST(self, root: Optional[TreeNode]) -> bool:
        def is_valid_helper(node: Optional[TreeNode],
            left_limit: int, right_limit: int) -> bool:
            if node is None:
                return True
            if not left_limit < node.val < right_limit:
                return False
            return (
                is_valid_helper(node.left, left_limit, node.val)
                and is_valid_helper(node.right, node.val, right_limit)
            )

        return is_valid_helper(root, -math.inf, math.inf)
```

## step2
### 読んだコード
- https://github.com/TORUS0818/leetcode/pull/30/files#r1712609142
- https://github.com/nittoco/leetcode/pull/35/files
- https://github.com/YukiMichishita/LeetCode/pull/8/files

### 感想
- 方針として思い浮かぶべきことが豊富な問題で、まだまだ知識不足を感じた
  - 方針のパターン
    - 「左の子はそのノードより小さい値」「右の子はそのノードより大きい値」というルールを応用し、あるノードをみた時に正しい範囲におさまっているかを確認する
      - これは、常識の範囲で再帰、stack、dequeでの方針をかける必要がある
    - in-orderで走査した際に昇順になっているかを確認する（比較しながら進めるパターンと走査した結果をみていくパターンがある）
      - これも常識の範囲内で、再帰orイテレーティブで作ったリストをチェックする方法、再帰+ジェネレーターを使ってチェックする方法を連想できると良さそう
    - Morris in-order traversal
      - これは名前を知っているくらいで良さそう

- 大規模な木が与えられても再帰の制限を気にしなくて済む実装が良さそう
  - このコードが使われる場面は、何らかのBSTに処理を加えていくときに、入力チェックとして使われると思うので

- math.infはマジックナンバーの印象を与えそうなので変えた方が良さそう。
  - math.infはfloatなので、`sys.maxsize`を使った方がintとしてnode.valと型があっていて良さそう。
  - https://docs.python.org/ja/3/library/sys.html#sys.maxsize

    > Py_ssize_t 型の変数が取りうる最大値を示す整数です。通常、32 ビットプラットフォームでは 2**31 - 1、64 ビットプラットフォームでは 2**63 - 1 になります。

- `pre-order`や`post-order`は名前を付けるほど重要な概念なのだろうか

- math.infがfloat('inf')を表し、'inf'が何を表すか気になってcptyhonを見にいった。
- Py_INFINITYという定数として'inf'を扱っているっぽい。
  - python3.14からの変更で、今までは Py_HUGE_VALとしているようだった。
  - https://github.com/python/cpython/pull/120027/files

    > #define INF Py_INFINITY


##### 範囲チェックをループで行う
- 再帰の解法と読みやすさはあまり変わらない印象
- rangesは雑かも

```python
class Solution:
    def isValidBST(self, root: Optional[TreeNode]) -> bool:
        MIN_VALUE, MAX_VALUE = -sys.maxsize, sys.maxsize
        node_and_ranges = [(root, MIN_VALUE, MAX_VALUE)]
        while node_and_ranges:
            node, left_limit, right_limit = node_and_ranges.pop()
            if not left_limit < node.val < right_limit:
                return False
            if node.left is not None:
                node_and_ranges.append((node.left, left_limit, node.val))
            if node.right is not None:
                node_and_ranges.append((node.right, node.val, right_limit))
        return True
```

##### in-orderで走査しながら昇順になっているかをチェックしていく
- 再帰の方がシンプルだが、絵で書いた時の動きはこちらの方が理解しやすい気がする

```python
class Solution:
    def isValidBST(self, root: Optional[TreeNode]) -> bool:
        MIN_VALUE = -sys.maxsize
        nodes = []
        current = root
        prev_value = MIN_VALUE
        while current is not None or nodes:
            while current is not None:
                nodes.append(current)
                current = current.left
            node = nodes.pop()
            if not prev_value < node.val:
                return False
            prev_value = node.val
            current = node.right
        return True
```

##### generatorを使ってin-orderで走査した結果をループでみていく
- 再帰を使ってしまうが、generatorになれるため書いてみる
- 形としては一番シンプルで好み
- 今回のように規則性を持った走査の結果を使いたい時は使えそう

```python
class Solution:
    def isValidBST(self, root: Optional[TreeNode]) -> bool:
        MIN_VALUE = -sys.maxsize
        def inorder_sort(node: Optional[TreeNode]) -> int:
            if node is None:
                return 
            yield from inorder_sort(node.left)
            yield node
            yield from inorder_sort(node.right)

        prev_value = MIN_VALUE       
        for node in inorder_sort(root):
            if not prev_value < node.val:
                return False
            prev_value = node.val
        return True
```

## step3
- 途中で番兵で書けば楽だと気づいて、step1の再帰をstackに直したものが最終的に気に入った

```python
class Solution:
    def isValidBST(self, root: Optional[TreeNode]) -> bool:
        MIN_VAL, MAX_VAL = -sys.maxsize, sys.maxsize
        node_from_to = [(root, MIN_VAL, MAX_VAL)]
        while node_from_to:
            node, left_limit, right_limit = node_from_to.pop()
            if node is None:
                continue
            if not left_limit < node.val < right_limit:
                return False
            node_from_to.append((node.left, left_limit, node.val))
            node_from_to.append((node.right, node.val, right_limit))
        return True
```