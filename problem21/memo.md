## 取り組み方
- step1: 5分以内に空で書いてAcceptedされるまで解く
- step2: discord内で他の方のコードを5人分読む + 何をよい/悪いと思ったか、何を避けたいと思ったか等感じたことを3つ以上かく
- step3: 10分以内に1回もエラーを出さずに3回連続で解く

## step1
### 考えたこと
ある深さの頂点をすべて見て子があれば訪問キューにためていく形のBFSで解けるはず。
DFSで最も深かった時の値を返すでも実装できるが、今回はBFSの方が自分的には直感的なので、 BFSで実装する。

時間計算量は辺の数だけ探索するからO(n)、空間計算量は幅の最大サイズなのでO(n)で問題なさそう。

### 感想

```python
class Solution:
    def maxDepth(self, root: Optional[TreeNode]) -> int:
        if root is None:
            return 0
        nodes = [root]
        depth = 0
        while nodes:
            depth += 1
            next_nodes = []
            for node in nodes:
                if node.left is not None:
                    next_nodes.append(node.left)
                if node.right is not None:
                    next_nodes.append(node.right)
            nodes = next_nodes

        return depth
```

## step2
### 読んだコード
- https://github.com/Mike0121/LeetCode/pull/6/files?short_path=7f7ae56#diff-7f7ae5605b4880b2e3148c6f41497201ed08f751754c1e8842c80e2d3fd63027
- https://github.com/nittoco/leetcode/pull/14/files?short_path=ca7b920#diff-ca7b920eac4ca0877278ca7c8a3592fa01e9c49ee0f5babe84fb558bba595552
- https://github.com/fhiyo/leetcode/pull/23/files?short_path=b0f2a6c#diff-b0f2a6c61fc63ccb00b41f4b6190b762f5ae05a4bb504f316607886dc390bb70
- https://github.com/hayashi-ay/leetcode/pull/22/files
- https://github.com/fuga-98/arai60/pull/21/files?short_path=e8877b0#diff-e8877b0cb6e07cec83fae8df717606e29b78a217513e339eb7e378e275360b07

### 読んだ感想
似たようなコードが多いだろうとと思っていたが、実際はバリュエーション豊かで面白かった。

if not と is not None だが、今回は後者の方が理解しやすいと感じた。また、統一されていない場合は気になった。

再帰とイテレーティブの比較は、イテレーティブの方がコードの長さは長くなるが、紙に書いたりせず理解できると思った。

```python
class Solution:
    def maxDepth(self, root: Optional[TreeNode]) -> int:
        if root is None:
            return 0
        left_depth = self.maxDepth(root.left)
        right_depth = self.maxDepth(root.right)
        return max(left_depth, right_depth) + 1
```

## step3
### 感想
bfsを実装するときに、
「異常系をさばく -> 変数を定義する -> ループにはいる
    -> 深さをインクリメントする -> 次の遷移先を探す -> 遷移先リストを探索リストに渡す」
の型が掴めてき他のか、step1からすんなり書けてよかった。

```python
class Solution:
    def maxDepth(self, root: Optional[TreeNode]) -> int:
        if root is None:
            return 0
        nodes = [root]
        depth = 0
        while nodes:
            depth += 1
            next_nodes = []
            for node in nodes:
                if node.left is not None:
                    next_nodes.append(node.left)
                if node.right is not None:
                    next_nodes.append(node.right)
            nodes = next_nodes
        return depth
``
