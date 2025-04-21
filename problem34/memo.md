## 取り組み方
- step1: 5分以内に空で書いてAcceptedされるまで解く + テストケースと関連する知識を連想してみる
- step2: 他の方の記録を読んで連想すべき知識や実装を把握した上で、前提を置いた状態で最適な手法を選択し実装する
- step3: 10分以内に1回もエラーを出さずに3回連続で解く

## step1
2次元配列のsum_pathsを基本的に、`sum_paths[r][c] = sum_paths[r-1][c] + sum_paths[r][c-1]`として考える。
今の座標に障害物がない時は上と左の和を組み合わせの数として、障害物があるときは0とすれば良いだけ。

```python
class Solution:
    def uniquePathsWithObstacles(self, obstacleGrid: List[List[int]]) -> int:
        OBSTACLE = 1
        if obstacleGrid[0][0] == OBSTACLE:
            return 0

        width = len(obstacleGrid[0])
        height = len(obstacleGrid)
        sum_paths = [[0] * width for _ in range(height)]
        sum_paths[0][0] = 1
        for h in range(1, height):
            if obstacleGrid[h][0] == OBSTACLE:
                continue
            sum_paths[h][0] = sum_paths[h - 1][0]
        for w in range(1, width):
            if obstacleGrid[0][w] == OBSTACLE:
                continue
            sum_paths[0][w] = sum_paths[0][w - 1]
        
        for h in range(1, height):
            for w in range(1, width):
                if obstacleGrid[h][w] == OBSTACLE:
                    continue
                sum_paths[h][w] = sum_paths[h - 1][w] + sum_paths[h][w - 1]
        return sum_paths[-1][-1]
```

## step2
### 読んだコード
- https://github.com/hayashi-ay/leetcode/pull/44/files
- https://github.com/SuperHotDogCat/coding-interview/pull/17/files
- https://github.com/sakupan102/arai60-practice/pull/35/files

### 感想
- h>0, w>0 に加算を分けるとかなりスッキリ変形できて良い
- 地味なところだが、 意図がない限りは height -> width の順で操作することが統一されてる方が適切だと思った

```python
class Solution:
    def uniquePathsWithObstacles(self, obstacleGrid: List[List[int]]) -> int:
        OBSTACLE = 1
        if obstacleGrid[0][0] == OBSTACLE:
            return 0

        height, width = len(obstacleGrid), len(obstacleGrid[0])
        sum_paths = [[0] * width for _ in range(height)]
        sum_paths[0][0] = 1
        for h in range(height):
            for w in range(width):
                if obstacleGrid[h][w] == OBSTACLE:
                    continue
                if h > 0:
                    sum_paths[h][w] += sum_paths[h - 1][w]
                if w > 0:
                    sum_paths[h][w] += sum_paths[h][w - 1]
        return sum_paths[-1][-1]
```

## step3
```python
class Solution:
    def uniquePathsWithObstacles(self, obstacleGrid: List[List[int]]) -> int:
        OBSTACLE = 1
        if obstacleGrid[0][0] == OBSTACLE:
            return 0
        num_rows = len(obstacleGrid)
        num_cols = len(obstacleGrid[0])
        sum_paths = [[0] * num_cols for _ in range(num_rows)]
        sum_paths[0][0] = 1
        for row in range(num_rows):
            for col in range(num_cols):
                if obstacleGrid[row][col] == OBSTACLE:
                    continue
                if row > 0:
                    sum_paths[row][col] += sum_paths[row - 1][col]
                if col > 0:
                    sum_paths[row][col] += sum_paths[row][col - 1]
        return sum_paths[num_rows - 1][num_cols - 1]
```
