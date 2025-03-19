## 取り組み方
- step1: 5分以内に空で書いてAcceptedされるまで解く
- step2: discord内で他の方のコードを5人分読む + 何をよい/悪いと思ったか、何を避けたいと思ったか等感じたことを3つ以上かく
- step3: 10分以内に1回もエラーを出さずに3回連続で解く

## step1
### 考えたこと
配列の中央の要素で区切れば、左右ほぼ均等に(要素数が偶数の時だけ左右のどちらかが1多い)分かれる。
中央の値を根のvalにして、左側の要素群と右側の要素群も中央の値を根のvalにする操作を再帰的に行なっていけば、Height-balancedな二分木ができる。

ベースケース：配列が空ならNoneを返す。
配列の中央の要素を根にする。
左側の[:mid]の配列を使用して左部分木を再帰的に作る。
右側の[mid+1:]の配列を使用して右部分木を再帰的に作る。

時間計算量は配列の要素数分だけ再帰するのでO(N)。
空間計算量は配列の要素数のサイズの木ができるのでO(N)。

練習のため、stackの書き換えも行う。

##### 再帰
```python
class Solution:
    def sortedArrayToBST(self, nums: List[int]) -> Optional[TreeNode]:
        if nums is None:
            return None

        def construct_BST(left: int, right: int) -> Optional[TreeNode]:
            if left > right:
                return None
            middle = (left + right) // 2
            node = TreeNode(nums[middle])
            node.left = construct_BST(left, middle - 1)
            node.right = construct_BST(middle + 1, right)
            return node
        
        return construct_BST(0, len(nums) - 1)
```

##### stack
```python
class Solution:
    def sortedArrayToBST(self, nums: List[int]) -> Optional[TreeNode]:
        if nums is None:
            return None
        bst = TreeNode()
        stack = [(0, len(nums)-1, bst)]
        while stack:
            left, right, node = stack.pop()
            middle = (left + right) // 2
            node.val = nums[middle]
            if left < middle:
                node.left = TreeNode()
                stack.append((left, middle - 1, node.left))
            if middle < right:
                node.right = TreeNode()
                stack.append((middle + 1, right, node.right))
        return bst
```

## step2
### 読んだコード
- https://github.com/t0hsumi/leetcode/pull/24/files
- https://github.com/SuperHotDogCat/coding-interview/pull/40/files
- https://github.com/t0hsumi/leetcode/pull/24/files
- https://github.com/fhiyo/leetcode/pull/26/files
- https://github.com/sakupan102/arai60-practice/pull/25

### 読んだ感想
右端を開区間にする人もちらほらいた。自分は閉区間にした方が左右対称な感じがして好み。

インデックスを管理しながら再帰するパターンと、配列を管理しながら再帰するパターンも見かけた。
後者のやり方では、コピーコストがかかることを知った。

そもそもこの解法って証明とかいるんだろうか。

```python
class Solution:
    def sortedArrayToBST(self, nums: List[int]) -> Optional[TreeNode]:
        if nums is None:
            return None

        def construct_BST(left: int, right: int) -> Optional[TreeNode]:
            if left > right:
                return None
            middle = (left + right) // 2
            root = TreeNode(nums[middle])
            root.left = construct_BST(left, middle - 1)
            root.right = construct_BST(middle + 1, right)
            return root
        
        return construct_BST(0, len(nums) - 1)
```

## step3
### 感想
- 平衡二分木で調べたらよくAVL木や赤黒木がヒットしたので、何するものかくらいは押さえておく。

```python
class Solution:
    def sortedArrayToBST(self, nums: List[int]) -> Optional[TreeNode]:
        if nums is None:
            return None
        
        def construct_BST(left: int, right: int) -> Optional[TreeNode]:
            if left > right:
                return None
            middle = (left + right) // 2
            root = TreeNode(nums[middle])
            root.left = construct_BST(left, middle - 1)
            root.right = construct_BST(middle + 1, right)
            return root
        
        return construct_BST(0, len(nums) - 1)
```