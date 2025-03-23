## 取り組み方
- step1: 5分以内に空で書いてAcceptedされるまで解く
- step2: discord内で他の方のコードを5人分読む + 何をよい/悪いと思ったか、何を避けたいと思ったか等感じたことを3つ以上かく
- step3: 10分以内に1回もエラーを出さずに3回連続で解く

## step1
### 考えたこと
深さごとに節点をみていくだけ。とりあえず、BFSが思いつく。
dequeを使うよりlistを使っていく方針の方が、あるレベルの節点をまとめた結果を答えに足して行けそう。

DFSで現在の深さを管理しながら探索していく方法も考えたが、まあ、これはBFSが想定解で良いのでは。

```python
class Solution:
    def levelOrder(self, root: Optional[TreeNode]) -> List[List[int]]:
        values = []
        if root is None:
            return values
        nodes = [root]
        while nodes:
            current_level_values = []
            next_nodes = []
            for node in nodes:
                current_level_values.append(node.val)
                if node.left is not None:
                    next_nodes.append(node.left)
                if node.right is not None:
                    next_nodes.append(node.right)
            values.append(current_level_values)
            nodes = next_nodes     
        return values
```

## step2
### 読んだコード
- https://github.com/Mike0121/LeetCode/pull/7/files
- https://github.com/fhiyo/leetcode/pull/28/files
- https://github.com/TORUS0818/leetcode/pull/28/files
- https://github.com/TORUS0818/leetcode/pull/28/files
- https://github.com/Ryotaro25/leetcode_first60/pull/28/files
- https://github.com/t0hsumi/leetcode/pull/26/files

### 読んだ感想
valuesは説明しなさすぎかも知れない。level_ordered_valuesくらいか。

currentを付けなくても今注目していることはわかるので要らない。総じて、変数名に改善の余地がまだまだありそう。

子ノードをappendする部分は関数化しても良いかも知れない。

答えの作り方の実装の部分で、
以下のように「空のリストを答えのリストにappendして、答えのリストの末端のリストにnode.valを追加していく」
やり方は思い付けていなかった。

```python
        while nodes:
            level_ordered_values.append([])
            for node in nodes:
                level_ordered_values[-1].append(node.val)
```

こちらの方法だと、current_level_valuesがいらずに済む。
どちらの方が分かりやすいかは5分5分か。

子ノードがNoneでも構わず突っ込んで、```nodes = list(filter(None, nodes))```するやり方もあることを知れた。

```python
class Solution:
    def levelOrder(self, root: Optional[TreeNode]) -> List[List[int]]:
        if root is None:
            return []
        level_ordered_values = []
        nodes = [root]
        while nodes:
            level_ordered_values.append([])
            next_nodes = []           
            for node in nodes:
                level_ordered_values[-1].append(node.val)
                next_nodes.append(node.left)
                next_nodes.append(node.right)
            next_nodes = list(filter(None, next_nodes))
            nodes = next_nodes        
        return level_ordered_values
```

## step3
### 感想
Treeに関する問題が6問目だったからか、サクサク進んだ。

```python
class Solution:
    def levelOrder(self, root: Optional[TreeNode]) -> List[List[int]]:
        if root is None:
            return []
        nodes = [root]
        level_ordered_values = []
        while nodes:
            next_nodes = []
            level_ordered_values.append([])
            for node in nodes:
                level_ordered_values[-1].append(node.val)
                if node.left is not None:
                    next_nodes.append(node.left)
                if node.right is not None:
                    next_nodes.append(node.right)
            nodes = next_nodes
        return level_ordered_values
```