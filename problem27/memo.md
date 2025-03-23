## 取り組み方
- step1: 5分以内に空で書いてAcceptedされるまで解く
- step2: discord内で他の方のコードを5人分読む + 何をよい/悪いと思ったか、何を避けたいと思ったか等感じたことを3つ以上かく
- step3: 10分以内に1回もエラーを出さずに3回連続で解く

## step1
### 考えたこと
深さごとに節点の値をまとめて、深さが偶数番目の時はまとめたものを反転させる。
幅優先探索で解ける。

```python
class Solution:
    def zigzagLevelOrder(self, root: Optional[TreeNode]) -> List[List[int]]:
        if root is None:
            return []
        zigzag_values = []
        nodes = [root]
        level = 1
        while nodes:
            next_nodes = []
            level_values = []
            for node in nodes:
                level_values.append(node.val)
                if node.left is not None:
                    next_nodes.append(node.left)
                if node.right is not None:
                    next_nodes.append(node.right)
            if level % 2 == 0:
                level_values.reverse()
            zigzag_values.append(level_values)
            level += 1
            nodes = next_nodes
        return zigzag_values
```

## step2
### 読んだコード
- https://github.com/fuga-98/arai60/pull/27/files
- https://github.com/olsen-blue/Arai60/pull/27/files
- https://github.com/TORUS0818/leetcode/pull/29/files
- https://github.com/fhiyo/leetcode/pull/29/files
- https://github.com/YukiMichishita/LeetCode/pull/11/files

### 読んだ感想
反転させるかの管理は、
①levelを使って偶奇を判定する、②boolで管理する、③-1をかけてプラマイで管理するパターンがある。
一番頭を使わなくていいのは①と思っていたが、命名によっては②が一番良いかも。

zigzag_values は命名をサボりすぎかもしれない。
zigzag_level_ordered_valuesだと長すぎる気もするので悩ましい。

反転を使わずにzigzagに探索していく解法もあったが、個人的には反転の方が素直な感じがする。

だいぶ細かいが、levelとnodeの定義順と更新順は揃っている方が綺麗。

```python
class Solution:
    def zigzagLevelOrder(self, root: Optional[TreeNode]) -> List[List[int]]:
        if root is None:
            return []
        zigzag_level_ordered_values = []
        left_to_right = True  # to create a zigzag
        nodes = [root]
        while nodes:
            level_values = []
            next_nodes = []
            for node in nodes:
                level_values.append(node.val)
                if node.left:
                    next_nodes.append(node.left)
                if node.right:
                    next_nodes.append(node.right)
            if not left_to_right:
                level_values.reverse()
            zigzag_level_ordered_values.append(level_values)
            left_to_right = not left_to_right
            nodes = next_nodes
        return zigzag_level_ordered_values
```

## step3
```python
class Solution:
    def zigzagLevelOrder(self, root: Optional[TreeNode]) -> List[List[int]]:
        if root is None:
            return []
        zigzag_level_ordered_values = []
        left_to_right = True
        nodes = [root]
        while nodes:
            next_nodes = []
            level_values = []
            for node in nodes:
                level_values.append(node.val)
                if node.left is not None:
                    next_nodes.append(node.left)
                if node.right is not None:
                    next_nodes.append(node.right)
            if not left_to_right:
                level_values.reverse()
            zigzag_level_ordered_values.append(level_values)
            left_to_right = not left_to_right
            nodes = next_nodes
        return zigzag_level_ordered_values
```

## step4
余裕があったのでlistのreverseの実装をcpythonで確認した。
listのサイズが1の時は何もしなくて、2以上ならreverse_sliceという関数を呼び出し、
listの先頭と末端に注目して、swapしていくという操作をしていることがわかった。

https://github.com/python/cpython/blob/main/Objects/listobject.c#L3169
https://github.com/python/cpython/blob/main/Objects/listobject.c#L1555

### 感想
思っていたより簡単に読めるのかもしれない。
