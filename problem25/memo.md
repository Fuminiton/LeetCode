## 取り組み方
- step1: 5分以内に空で書いてAcceptedされるまで解く
- step2: discord内で他の方のコードを5人分読む + 何をよい/悪いと思ったか、何を避けたいと思ったか等感じたことを3つ以上かく
- step3: 10分以内に1回もエラーを出さずに3回連続で解く

## step1
### 考えたこと
根から葉に向かって、節点を見ていく。
以下のように幅優先探索をする。

1. 節点の訪問リストを一つずつ見ていく
2. 節点までの和がtargetSumと一致するかをチェックする。
3. 子があれば、子の値を現在までの値を足した上で探索先とする。
4. 次の探索先リストを訪問リストに渡す

```python
class Solution:
    def hasPathSum(self, root: Optional[TreeNode], targetSum: int) -> bool:
        if root is None:
            return False
        node_and_path_sums = [(root, root.val)]

        def is_leaf(node: Optional[TreeNode]) -> bool:
            if node.left is None and node.right is None:
                return True
            return False

        while node_and_path_sums:
            next_node_and_path_sums = []
            for node, path_sum in node_and_path_sums:
                if is_leaf(node) and path_sum == targetSum:
                    return True
                if node.left is not None:
                    next_node_and_path_sums.append((
                        node.left, path_sum + node.left.val
                    ))
                if node.right is not None:
                    next_node_and_path_sums.append((
                        node.right, path_sum + node.right.val
                    ))
            node_and_path_sums = next_node_and_path_sums
        return False
```

## step2
### 読んだコード
- https://github.com/Ryotaro25/leetcode_first60/pull/27/files
- https://github.com/shining-ai/leetcode/pull/25/files
- https://github.com/sakupan102/arai60-practice/pull/26/files
- https://github.com/fhiyo/leetcode/pull/27/files
- https://github.com/kazukiii/leetcode/pull/26/files


### 読んだ感想
path_sumの足すタイミングは、nodeをnode_and_path_sumsから取り出した際に行う方が自然かと思った。

結局、path_sumをタプルで持っているので、別にBFSである必要がない。stackでdfsの方がシンプル。

DFSを使って途中で戻りながらpath_sumを計算していく方法やtargetSum - node.val をターゲットとして渡す再帰の発想はなかった。

##### stack
```python
class Solution:
    def hasPathSum(self, root: Optional[TreeNode], targetSum: int) -> bool:
        if root is None:
            return False
        node_and_path_sums = [(root, 0)]

        def is_leaf(node: Optional[TreeNode]) -> bool:
            return node.left is None and node.right is None

        while node_and_path_sums:
            node, path_sum = node_and_path_sums.pop()
            path_sum += node.val
            if is_leaf(node) and path_sum == targetSum:
                    return True
            if node.left is not None:
                node_and_path_sums.append((node.left, path_sum))
            if node.right is not None:
                node_and_path_sums.append((node.right, path_sum))
        return False
```

##### 再帰
```python
class Solution:
    def hasPathSum(self, root: Optional[TreeNode], targetSum: int) -> bool:
        if root is None:
            return False
        if root.left is None and root.right is None:
            return root.val == targetSum
        return (
            self.hasPathSum(root.left, targetSum - root.val) or
            self.hasPathSum(root.right, targetSum - root.val)
        )
```

## step3
### 感想
BFSの処理の流れが気に入っているせいで、他方針の選択の妨げになっている気がする。

わかってしまえば、(target - node.val)をtargetとして再帰していくのが、一番自然な気がする。

```python
class Solution:
    def hasPathSum(self, root: Optional[TreeNode], targetSum: int) -> bool:
        if root is None:
            return False
        node_and_path_sums = [(root, 0)]
        
        def is_leaf(node: Optional[TreeNode]) -> bool:
            return node.left is None and node.right is None

        while node_and_path_sums:
            node, path_sum = node_and_path_sums.pop()
            path_sum += node.val
            if is_leaf(node) and targetSum == path_sum:
                return True
            if node.left is not None:
                node_and_path_sums.append((node.left, path_sum))
            if node.right is not None:
                node_and_path_sums.append((node.right, path_sum))
        
        return False
```