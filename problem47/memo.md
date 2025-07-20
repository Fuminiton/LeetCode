## 取り組み方
- step1: 5分以内に空で書いてAcceptedされるまで解く + テストケースと関連する知識を連想してみる
- step2: 他の方の記録を読んで連想すべき知識や実装を把握した上で、前提を置いた状態で最適な手法を選択し実装する
- step3: 10分以内に1回もエラーを出さずに3回連続で解く

## step1
求めたい木について、targetより大きい方をbigger、target以下をsmallerとすると、
上から木を見ていった時に、頂点の値によって以下の情報が確定していく。

現在見ている頂点の値が

- targerより大きい場合、
  - 現在の頂点と右部分木の要素は全てはtargetより大きい ~ biggerの要素
  - 一方、左部分木はtargetより大きいもの、以下のものがある ~ bigger.leftの要素と、smallerの要素があるかもしれない
    - ので再度、左部分木の頂点から調べていく必要がある ~ 再帰
- target以下の場合、
  - 現在の頂点と左部分木の要素は全てはtarget以下である
  - 一方、右部分木はtargetより大きいもの、以下のものがある ~ smaller.rightの要素と、biggerの要素があるかもしれない
    - ので再度、右部分木の頂点から調べていく必要がある ~ 再帰

となっていくので、これらの確定した情報を元にbigger, smallerの木を構築していけばよい。

```py
class Solution:
    def splitBST(self, root: Optional[TreeNode], target: int) -> List[Optional[TreeNode]]:
        def helper(node: Optional[TreeNode]) -> List[Optional[TreeNode]]:
            if node is None:
                return [None, None]

            smaller = TreeNode()
            bigger = TreeNode()
            if node.val <= target:
                smaller.val = node.val
                smaller.left = node.left
                smaller.right, bigger = helper(node.right)
            else:
                bigger.val = node.val
                bigger.right = node.right
                smaller, bigger.left = helper(node.left)

            return [smaller, bigger]
  
        return helper(root)
```

## step2
### 読んだ
- https://github.com/hayashi-ay/leetcode/pull/53/files
- https://github.com/TORUS0818/leetcode/pull/49/files
- https://github.com/ryosuketc/leetcode_arai60/pull/36/files

### 感想
- ヘルパー関数の名前が雑すぎるかもしれない。
  - そもそも、ヘルパー関数使わずに再帰する人が多そう。
- targetと頂点の値の大小関係に応じて処理を分岐し、2つのサブツリーを返すというのは全員共通だが、更新の仕方にはバリュエーションがあった
  - 「確定していない側のサブツリーの要素を再帰的に探す」のと、再帰的に探す前に、確定している部分だけを確定したと表現してから再帰的に探すパターン
  - 自分は後者を選んだが、これは手作業でやりたいことを書いた時の流れなので、後者が直感的なのでは

```py
class Solution:
    def splitBST(self, root: Optional[TreeNode], target: int) -> List[Optional[TreeNode]]:
        if root is None:
            return [None, None]

        if root.val <= target:
            smaller = TreeNode(root.val)
            smaller.left = root.left
            smaller.right, larger = self.splitBST(root.right, target)
            return [smaller, larger]
        else:
            larger = TreeNode(root.val)
            smaller, larger.left = self.splitBST(root.left, target)
            larger.right = root.right
            return [smaller, larger]
```

## step3

```py
class Solution:
    def splitBST(self, root: Optional[TreeNode], target: int) -> List[Optional[TreeNode]]:
        def split_bst_helper(node: Optional[TreeNode]) -> List[Optional[TreeNode]]:
            if node is None:
                return [None, None]
            if node.val <= target:
                smaller = node
                smaller.right, bigger = split_bst_helper(node.right)
                return smaller, bigger
            else:
                bigger = node
                smaller, bigger.left = split_bst_helper(node.left)
                return smaller, bigger
        
        copied_root = deepcopy(root)
        return split_bst_helper(copied_root)
```
