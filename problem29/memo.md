## 取り組み方
- step1: 5分以内に空で書いてAcceptedされるまで解く + テストケースと関連する知識を連想してみる
- step2: 他の方の記録を読んで連想すべき知識や実装を把握した上で、前提を置いた状態で最適な手法を選択し実装する
- step3: 10分以内に1回もエラーを出さずに3回連続で解く

## step1
### 考えたこと
preorderの順序でBSTを構成していく。
node -> left -> right の順で見ていくのがpreorderで、
inorderも与えられているので、node.valがinorderのどの位置にあるかを探して、
inorderの左側が左部分木の要素で、右側が右部分木の要素であるから再帰的にこれを繰り返していけばBSTができる。

引数はinorderの左端と右端のインデックス。ここは配列を渡して構成していく方法もありそう。
ベースケースは左端が右端より大きくなる時で、Noneを返す。
初めにpreorderの現在の値を用いてTreeNodeを構築する。そのあと、preorderをインクリメントする。
次に、先ほど探した値がinorderのどこの位置にあるのかを探す。list.indexやdefaultdictが使えそう。
あとは、左側、右側の順番で部分木を構築するように、左端と先ほど探したインデックス-1、インデックス+1と右端を渡して再帰する。
最後に構成した木を返す。

再帰をまず書いた上で、ループでスラスラ書き変えたい。

##### 再帰
```python
class Solution:
    def buildTree(self, preorder: List[int], inorder: List[int]
            ) -> Optional[TreeNode]:
        val_to_index = defaultdict(int)
        for index, val in enumerate(inorder):
            val_to_index[val] = index
        preorder_val = iter(preorder)

        def build_binary_tree_helper(left: int, right: int            
                ) -> Optional[TreeNode]:
            if left > right:
                return None
            root_val = next(preorder_val)
            root = TreeNode(root_val)
            deviding_position = val_to_index[root_val]
            root.left = build_binary_tree_helper(
                left, deviding_position - 1)
            root.right = build_binary_tree_helper(
                deviding_position + 1, right)
            return root

        return build_binary_tree_helper(0, len(preorder) - 1)
```

##### ループ
- `if left > right`の部分を分解してstackに入れる段階で弾くようにしているので、完全な書き換えではなさそう。

```python
class Solution:
    def buildTree(self, preorder: List[int], inorder: List[int]
            ) -> Optional[TreeNode]:
        val_to_index = defaultdict(int)
        for i, val in enumerate(inorder):
            val_to_index[val] = i
        
        node_val = iter(preorder)
        root = TreeNode()
        nodes_and_ranges = [(root, 0, len(preorder) - 1)]
        while nodes_and_ranges:
            node, left, right = nodes_and_ranges.pop()
            node.val = next(node_val)
            deviding_position = val_to_index[node.val]
            if deviding_position + 1 <= right:
                node.right = TreeNode()
                nodes_and_ranges.append((node.right, deviding_position + 1, right))
            if left <= deviding_position - 1:
                node.left = TreeNode()
                nodes_and_ranges.append((node.left, left, deviding_position - 1))
        return root
```

## step2
### 読んだコード
- https://github.com/hayashi-ay/leetcode/pull/43/files
- https://github.com/fhiyo/leetcode/pull/31/files
- https://github.com/nittoco/leetcode/pull/37/files
- https://github.com/fuga-98/arai60/pull/29/files

### 感想
- `val_to_index`より`val_to_inorder_index`とかの方が親切
- `iter(preorder)`の部分は`nonlocal`でpreprderのインデックスをもって管理する方が多数派だった
- inorderから構築していく方法もあるが、今回はpreorderから構築していく方法の方が理解はしやすそうな気がした
  - 一方、「preorderなら今みている頂点以下の左部分木は探索済みである」など木の走査順の特徴を自然にでてくるくらい理解するのは重要だと思うので、後で書く

```python
class Solution:
    def buildTree(self, preorder: List[int], inorder: List[int]
            ) -> Optional[TreeNode]:
        val_to_inorder_index = {val: i for i, val enumerate(inorder)}
        preorder_val_iterator = iter(preorder)

        def build_binary_tree_helper(left: int, right: int            
                ) -> Optional[TreeNode]:
            if left > right:
                return None
            node_val = next(preorder_val_iterator)
            node = TreeNode(node_val)
            deviding_index = val_to_inorder_index[node_val]
            node.left = build_binary_tree_helper(
                left, deviding_index - 1)
            node.right = build_binary_tree_helper(
                deviding_index + 1, right)
            return node

        return build_binary_tree_helper(0, len(inorder) - 1)
```

## step3
- 再帰の回答の方が好みだが、再帰を書き終わった頃にはループに書き換えたくなる
- 次はコメントなり、感想なりを増やしていきたい

```python
class Solution:
    def buildTree(self, preorder: List[int], inorder: List[int]) -> Optional[TreeNode]:
        inorder_val_to_index = {val : index for index, val in enumerate(inorder)}
        preorder_iterator = iter(preorder)

        def build_binary_tree_helper(left: int, right: int) -> Optional[TreeNode]:
            if not left <= right:
                return None
            node_val = next(preorder_iterator)
            deviding_index = inorder_val_to_index[node_val]
            node = TreeNode(node_val)
            node.left = build_binary_tree_helper(left, deviding_index - 1)
            node.right = build_binary_tree_helper(deviding_index + 1, right)
            return node
        
        return build_binary_tree_helper(0, len(inorder) - 1)
```

```python
class Solution:
    def buildTree(self, preorder: List[int], inorder: List[int]) -> Optional[TreeNode]:
        value_to_index = {v : i for i, v in enumerate(inorder)}
        preorder_iter = iter(preorder)

        root = TreeNode()
        nodes_and_ranges = [(root, 0, len(inorder) - 1)]
        while nodes_and_ranges:
            node, left, right = nodes_and_ranges.pop()
            node_val = next(preorder_iter)
            deviding_index = value_to_index[node_val]
            node.val = node_val
            if deviding_index + 1 <= right:
                node.right = TreeNode()
                nodes_and_ranges.append((node.right, deviding_index + 1, right))
            if left <= deviding_index - 1:
                node.left = TreeNode()
                nodes_and_ranges.append((node.left, left, deviding_index - 1))
        
        return root
```