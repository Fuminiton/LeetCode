## 取り組み方
- step1: 5分以内に空で書いてAcceptedされるまで解く
- step2: discord内で他の方のコードを5人分読む + 何をよい/悪いと思ったか、何を避けたいと思ったか等感じたことを3つ以上かく
- step3: 10分以内に1回もエラーを出さずに3回連続で解く

## step1
### 考えたこと
深さごとにノードを見ていって、左右の子がないときの深さが答えになる。
したがって、BFSで根から子ノードを見ていって、左右の子があるかのチェックをする実装で解ける。

### 感想
```python
class Solution:
    def minDepth(self, root: Optional[TreeNode]) -> int:
        if root is None:
            return 0

        nodes = [root]
        depth = 0
        
        while nodes:
            depth += 1
            next_nodes = []
            for node in nodes:
                if node.left is None and node.right is None:
                    return depth
                if node.left is not None:
                    next_nodes.append(node.left)
                if node.right is not None:
                    next_nodes.append(node.right)
            nodes = next_nodes
```

## step2
### 読んだコード
- https://github.com/fhiyo/leetcode/pull/24/files
- https://github.com/sakupan102/arai60-practice/pull/23/files
- https://github.com/hayashi-ay/leetcode/pull/26/files
- https://github.com/seal-azarashi/leetcode/pull/21/files
- https://github.com/hroc135/leetcode/pull/21/files

### 読んだ感想
- 再帰とstackの書き換えがすらすら書けた方が良いらしい。
- 一応、答えが見つからなかった時のために、raiseを最後につけておくべきだった。
- depthの初期化を1にして、nodes = next_nodesの前にdepthを更新する方が自然な気がしてきた。

```python
class Solution:
    def minDepth(self, root: Optional[TreeNode]) -> int:
        if root is None:
            return 0
        if root.left is None and root.right is None:
            return 1
        depth = math.inf
        if root.left is not None:
            depth = min(depth, 1 + self.minDepth(root.left))
        if root.right is not None:
            depth = min(depth, 1 + self.minDepth(root.right))
        return depth
```

## step3
```python
class Solution:
    def minDepth(self, root: Optional[TreeNode]) -> int:
        if root is None:
            return 0
        
        nodes = [root]
        depth = 1
        while nodes:
            next_nodes = []
            for node in nodes:
                if node.left is None and node.right is None:
                    return depth
                if node.left is not None:
                    next_nodes.append(node.left)
                if node.right is not None:
                    next_nodes.append(node.right)
            depth += 1
            nodes = next_nodes
        raise Exception("Unreachable")
```