## 取り組み方
- step1: 5分以内に空で書いてAcceptedされるまで解く
- step2: discord内で他の方のコードを5人分読む + 何をよい/悪いと思ったか、何を避けたいと思ったか等感じたことを3つ以上かく
- step3: 10分以内に1回もエラーを出さずに3回連続で解く

## step1
### 考えたこと
dfsを使って、grid[r][c]が1で未訪問の時は加算して、そうでない時は0を加算するようにして解けそう。
再帰かstackで解けるが、再帰を使った方がシンプルに書けそうなので再帰でかく。
その際、コールスタック制限に気をつけたコードを書きたい。

#### 処理の流れ
gridを左上から右下に向かうように以下のループをする

1. 訪れたことを記録するようの行列を定義する
2. grid[r][c]が1でないならcontinue
3. grid[r][c]がすでに訪れていたらcontinue
4. grid[r][c]が1なら深さ優先探索を呼び出す
   1. gridの範囲外なら0を返す
   2. gridが1でないなら0を返す
   3. すでに訪れていれば0を返す
   4. grid[r][c]を訪れたことのチェック
   5. 1 + 上下左右の探索結果を返す
5. 4.の結果と答えとする値の大きい方を答えとする値にする

#### 計算量
時間がO(n^2)、空間もO(n^2)

```python
class Solution:
    def maxAreaOfIsland(self, grid: List[List[int]]) -> int:
        def get_island_size(r: int, c: int) -> int:
            if not (0 <= r < rows and 0 <= c < cols):
                return 0
            if grid[r][c] != 1:
                return 0
            if visited[r][c]:
                return 0
            visited[r][c] = True
            island_size = 1
            for delta_r, delta_c in directions:
                island_size += get_island_size(r + delta_r, c + delta_c)
            return island_size

        MAX_ROWS, MAX_COLS = 51, 51

        # 再帰によるコールスタック制限を避けるため、再帰呼び出し上限をgridの最大サイズまで上げる
        sys.setrecursionlimit(MAX_ROWS * MAX_COLS)

        rows, cols = len(grid), len(grid[0])
        visited = [[False] * cols for _ in range(rows)]
        max_island_size = 0
        directions = [(-1, 0), (1, 0), (0, -1), (0, 1)]
        for r in range(rows):
            for c in range(cols):
                if grid[r][c] != 1:
                    continue
                if visited[r][c]:
                    continue
                island_size = get_island_size(r, c)
                max_island_size = max(island_size, max_island_size)

        # コールスタック制限対策で設定した値をデフォルト値に戻す
        sys.setrecursionlimit(1000)

        return max_island_size
```

## step2
### 読んだコード
- https://github.com/goto-untrapped/Arai60/pull/31/files
- https://github.com/hayashi-ay/leetcode/pull/34/files
- https://github.com/fhiyo/leetcode/pull/21/files
- https://github.com/hayashi-ay/leetcode/pull/34/files
- https://github.com/nittoco/leetcode/pull/24/files
- https://github.com/Mike0121/LeetCode/pull/36/files

### 読んだ感想
- 関数は上の方にしているが、これくらいの長さだと、上から下に読めるコードに仕上げるために方が今回は良さそう
- gridを別の数値で訪問ずみを表すように変えていく方法もあるが、入力を書き換えている点に抵抗がある
- LAND = 1 を置き忘れたのは良くなかった

```python
class Solution:
    LAND = 1
    def maxAreaOfIsland(self, grid: List[List[int]]) -> int:
        MAX_NUM_ROWS = 51
        MAX_NUM_COLS = 51
        num_rows = len(grid)
        num_cols = len(grid[0])
        visited = [[False] * num_cols for _ in range(num_rows)]
        max_island_size = 0
        directions = [(-1, 0), (1, 0), (0, -1), (0, 1)]

        def caluculate_area(r: int, c: int) -> int:
            if not (0 <= r < num_rows and 0 <= c < num_cols):
                return 0
            if grid[r][c] != self.LAND:
                return 0
            if visited[r][c]:
                return 0
            visited[r][c] = True
            island_size = 1
            for delta_r, delta_c in directions:
                island_size += caluculate_area(r + delta_r, c + delta_c)
            return island_size

        # 再帰によるコールスタック制限を避けるため、再帰呼び出し上限をgridの最大サイズまで上げる
        sys.setrecursionlimit(MAX_NUM_ROWS * MAX_NUM_COLS)

        for r in range(num_rows):
            for c in range(num_cols):
                if visited[r][c]:
                    continue
                island_size = caluculate_area(r, c)
                max_island_size = max(island_size, max_island_size)

        # コールスタック制限対策で設定した値をデフォルト値に戻す
        sys.setrecursionlimit(1000)

        return max_island_size
```

## step3
```python
class Solution:
    LAND = 1
    def maxAreaOfIsland(self, grid: List[List[int]]) -> int:
        num_rows = len(grid)
        num_cols = len(grid[0])
        visited = [[False] * num_cols for _ in range(num_rows)]
        max_area_size = 0
        directions = [(-1, 0), (1, 0), (0, -1), (0, 1)]

        def get_area_size(r: int, c: int) -> int:
            visiting = []
            visiting.append((r,c)) 
            area_size = 0
            while visiting:
                current_r, current_c = visiting.pop()
                if not(0 <= current_r < num_rows and 0 <= current_c < num_cols):
                    continue
                if visited[current_r][current_c]:
                    continue
                if grid[current_r][current_c] != self.LAND:
                    continue
                area_size += 1
                visited[current_r][current_c] = True
                for delta_r, delta_c in directions:
                    next_r = current_r + delta_r
                    next_c = current_c + delta_c
                    visiting.append((next_r, next_c))
            return area_size

        for r in range(num_rows):
            for c in range(num_cols):
                if visited[r][c]:
                    continue
                if grid[r][c] != self.LAND:
                    continue
                area_size = get_area_size(r, c)
                max_area_size = max(max_area_size, area_size)
        
        return max_area_size
```

### 感想
- step3では、再帰1回、stack2回で練習したが、やはり再帰の方がシンプルに書けて好み
- 書き方は頭に入っているのに8分くらいかかってしまったので、そろそろタイピング速度の改善を真面目に取り組まないといけないかも