## 取り組み方
- step1: 5分以内に空で書いてAcceptedされるまで解く
- step2: discord内で他の方のコードを5人分読む + 何をよい/悪いと思ったか、何を避けたいと思ったか等感じたことを3つ以上かく
- step3: 10分以内に1回もエラーを出さずに3回連続で解く

## step1
### 考えたこと
#### 処理の流れ
任意の隣接行列の要素を grid[height][width] とする。
要素を一つずつ見ていく。
要素が陸でまだ訪れていなければ、深さ優先探索を行う。
深さ優先探索の処理では、

- 訪れた場所をマークする
- 上下左右の隣接する要素に対して、陸かつ未訪問の場所なら、その要素から探索を開始する

を行なっていく。
メイン処理から、深さ優先探索の関数を何回呼び出せたかが島の数になるはず。

#### 計算量
時間計算量は、メイン処理 m*nと、深さ優先探索の部分。
深さ優先探索の部分は盤面が全部陸の時、初めの1回だけi*j回の探索があってその後、訪問済みで1回しか呼びだされないので、
大体 O(m*n)くらいのはず。

空間計算量は、隣接行列のサイズで O(m*n)。

グラフの計算量の考え方に自信がないので、step2で丁寧に調べる。

#### 気をつけること
- 要素の範囲外アクセスがないか
- 再帰が無限ループしないか

```python
class Solution:
    def numIslands(self, grid: List[List[str]]) -> int:
        def visit_lands(h: int, w: int) -> None:
            if not (0 <= h < height and 0 <= w < width):
                return
            if visited[h][w] or grid[h][w] == WATER:
                return
            visited[h][w] = True
            visit_lands(h - 1, w)
            visit_lands(h + 1, w)
            visit_lands(h, w - 1)
            visit_lands(h, w + 1)
        
        LAND = '1'
        WATER = '0'
        height = len(grid)
        width = len(grid[0])        
        visited = [[False] * width for _ in range(height)]
        num_islands = 0
        
        for h in range(height):
            for w in range(width):
                if not visited[h][w] and grid[h][w] == LAND:
                    num_islands += 1
                    visit_lands(h, w)
        
        return num_islands
```

## step2
### 読んだコード
- https://github.com/sakupan102/arai60-practice/pull/18/files
- https://github.com/fhiyo/leetcode/pull/20/files
- https://github.com/kazukiii/leetcode/pull/18/files
- https://github.com/philip82148/leetcode-arai60/pull/12/files
- https://github.com/TORUS0818/leetcode/pull/19/files
- 

### 読んだ感想
- union-findの解法は微妙に常識から外れそう。一方、スタックを用いたDFSsは把握しておく必要がありそう。
- h,w と row, columnは迷ったが、後者の方が多数派な印象を受けた。
- 上下左右の移動を辞書やらで管理して、書いている例をいくつかみたが、個人的には、row+1のような形で書いてしまった方が直感的な気がした。
- 再帰によるdfsとstackによるdfsの違いを調べたら、コールスタック制限を知れた。
  - https://docs.python.org/3/library/sys.html#sys.setrecursionlimit
- pythonは1000回がデフォルトだそうなので、今回だと以下の蛇のような形になる直鎖状の島があるケースを考慮して、300*300/2 + 300/2より大きい5000くらいをリミットとして書いておく。

```
*****
----*
*****
*----
*****
```

```python
class Solution:
r        def in_grid(r: int, c: int) -> bool:
            return 0 <= r < row and 0 <= c < column

        def visit_islands(r: int, c: int) -> None:
            if not in_grid(r, c):
                return
            if visited[r][c]:
                return
            if grid[r][c] == WATER:
                return
            visited[r][c] = True
            visit_islands(r - 1, c)
            visit_islands(r + 1, c)
            visit_islands(r, c - 1)
            visit_islands(r, c + 1)

        sys.setrecursionlimit(5000)
        LAND = '1'
        WATER = '0'
        row = len(grid)
        column = len(grid[0])        
        visited = [[False] * column for _ in range(row)]
        num_islands = 0

        for r in range(row):
            for c in range(column):
                if visited[r][c]:
                    continue
                if grid[r][c] == LAND:
                    num_islands += 1
                    visit_islands(r, c)

        return num_islands
```

## step3
```python
class Solution:
    def numIslands(self, grid: List[List[str]]) -> int:
        def visit_islands(r: int, c: int):
            if not (0 <= r and r < row and 0 <= c and c < column):
                return
            if visited[r][c] or grid[r][c] != LAND:
                return
            visited[r][c] = True
            visit_islands(r - 1, c)
            visit_islands(r + 1, c)
            visit_islands(r, c - 1)
            visit_islands(r, c + 1)
        
        sys.setrecursionlimit(5000)
        LAND = '1'
        row = len(grid)
        column = len(grid[0])
        visited = [[False] * column for _ in range(row)]
        num_islands = 0
        for r in range(row):
            for c in range(column):
                if visited[r][c] or grid[r][c] != LAND:
                    continue
                visit_islands(r, c)
                num_islands += 1
        
        return num_islands
```
