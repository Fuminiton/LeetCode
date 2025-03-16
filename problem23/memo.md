## 取り組み方
- step1: 5分以内に空で書いてAcceptedされるまで解く
- step2: discord内で他の方のコードを5人分読む + 何をよい/悪いと思ったか、何を避けたいと思ったか等感じたことを3つ以上かく
- step3: 10分以内に1回もエラーを出さずに3回連続で解く

## step1
### 考えたこと
再帰で書くのがシンプルで良さそう。
「再帰の終了条件->再帰のメイン処理->次の再帰の呼び出し->結果の返却」の流れで考える。

再帰の終了条件は、root1,root2がどちらもnullの時、もしくは、どちらか一方がnullの時で、それぞれnullとどちらか一方のrootを返せばよい。
再帰のメイン処理では、merged treeの現在のvalが何なのかを計算する。
次の再帰の呼び出しは、現在のmergedの頂点からleftとrightがどうなっているかを探索する。
結果の返却として、mergedを返す。

練習のため、DFSの再帰の実装とスタックの実装それぞれで解く。

##### 再帰
```python
class Solution:
    def mergeTrees(self, root1: Optional[TreeNode], root2: Optional[TreeNode]) -> Optional[TreeNode]:
        if not root1 and not root2:
            return None
        if not root1:
            return root2
        if not root2:
            return root1
        merged = TreeNode(root1.val + root2.val)
        merged.left = self.mergeTrees(root1.left, root2.left)
        merged.right = self.mergeTrees(root1.right, root2.right)
        return merged
```

##### stack
```python
class Solution:
    def mergeTrees(self, root1: Optional[TreeNode], root2: Optional[TreeNode]) -> Optional[TreeNode]:
        if not root1 and not root2:
            return None
        if not root1:
            return root2
        if not root2:
            return root1
        
        merged = TreeNode()
        stack = [(root1, root2, merged)]
        while stack:
            node1, node2, merged_node = stack.pop()

            if node1 and node2:
                merged_node.val = node1.val + node2.val
            elif node1:
                merged_node.val = node1.val
            elif node2:
                merged_node.val = node2.val
            
            if (node1 and node1.left) or (node2 and node2.left):
                merged_node.left = TreeNode()
                stack.append((
                    node1.left if node1 else None,
                    node2.left if node2 else None,
                    merged_node.left
                ))
            if (node1 and node1.right) or (node2 and node2.right):
                merged_node.right = TreeNode()
                stack.append((
                    node1.right if node1 else None,
                    node2.right if node2 else None,
                    merged_node.right
                ))

        return merged
```

## step2
### 読んだコード
- https://github.com/rossy0213/leetcode/pull/12/files
- https://github.com/ryoooooory/LeetCode/pull/26/files
- https://github.com/fhiyo/leetcode/pull/25/files
- https://github.com/seal-azarashi/leetcode/pull/22/files
- https://github.com/Ryotaro25/leetcode_first60/pull/25/files

### 読んだ感想
再帰の方が自然に感じた。

一方で、簡単な実装だからというだけではなくて、合理的な理由も説明できるようにしていきたい。
観点は、可読性、探索対象の深度、メモリ使用量くらいか。
今回は、探索対象の深さが予測できてかつメモリの使用量も明示的に管理したいわけではないので、再帰で書いて読みやすさを重視したい。

https://github.com/seal-azarashi/leetcode/pull/22/files#r1778932434
> それで、どんなユースケースでこの mergeTrees は使われるんでしょうか。

root1を基準に更新していく解法が LeetCode 上の Solutions によく載っていたが、破壊的な実装なので微妙ではと思った。

```python
class Solution:
    def mergeTrees(self, root1: Optional[TreeNode], root2: Optional[TreeNode]) -> Optional[TreeNode]:
        if not root1 and not root2:
            return None
        if not root1 or not root2:
            return root1 if root1 else root2

        merged = TreeNode(root1.val + root2.val)
        merged.left = self.mergeTrees(root1.left, root2.left)
        merged.right = self.mergeTrees(root1.right, root2.right)
        return merged
```

## step3
```python
class Solution:
    def mergeTrees(self, root1: Optional[TreeNode], root2: Optional[TreeNode]) -> Optional[TreeNode]:
        if not root1 and not root2:
            return None
        if not root1:
            return root2
        if not root2:
            return root1
        merged = TreeNode(root1.val + root2.val)
        merged.left = self.mergeTrees(root1.left, root2.left)
        merged.right = self.mergeTrees(root1.right, root2.right)
        return merged        
```