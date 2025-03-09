## 取り組み方
- step1: 5分以内に空で書いてAcceptedされるまで解く
- step2: discord内で他の方のコードを5人分読む + 何をよい/悪いと思ったか、何を避けたいと思ったか等感じたことを3つ以上かく
- step3: 10分以内に1回もエラーを出さずに3回連続で解く

## step1
### 考えたこと
基本的には隣接行列の処理の流れと同じ。

#### 処理の流れ
訪れた頂点をマークし、隣接している頂点をマークしていく再帰関数を用意する。
メイン関数から未訪問の頂点を引数に再帰関数を呼び出す。
メイン関数から何回再帰関数を呼び出せたかをカウントしていけば良い。

#### 計算量
時間計算量は、n + edges.length。空間計算量も、同様。

```python
class Solution:
    def countComponents(self, n: int, edges: List[List[int]]) -> int:
        graph = [[] for _ in range(n)]  # 全ての頂点について空のリストを用意
        visited = [False] * n
        components_count = 0

        for from_, to_ in edges:
            graph[from_].append(to_)
            graph[to_].append(from_)
        
        def search_connected_component(from_: int) -> None:
            if visited[from_]:
                return 
            visited[from_] = True
            for to_ in graph[from_]:
                search_connected_component(to_)

        for from_ in range(n):
            if visited[from_]:
                continue
            components_count += 1
            search_connected_component(from_)
        
        return components_count
```


## step2
### 読んだコード
- https://github.com/sakupan102/arai60-practice/pull/22/files
- https://github.com/hayashi-ay/leetcode/pull/37/files
- https://github.com/Ryotaro25/leetcode_first60/pull/20/files
- https://github.com/tarinaihitori/leetcode/pull/19/files
- https://github.com/Hurukawa2121/leetcode/pull/19/files
- https://github.com/colorbox/leetcode/pull/33/files



### 読んだ感想
いつもより命名に迷ってしまった。

関数呼び出しがコストがかかるものだという意識は希薄だったと思う。

- https://github.com/sakupan102/arai60-practice/pull/22/files

> 訪れているかの判定を次の再帰に任せた場合、やや処理速度が落ちる点は気にしたほうが良いと思います。理由は、関数の呼び出しが、一般的に、コストがかかる処理だからです。個人的には再帰関数呼び出しの前に判定したほうが良いと思います。

indexの複数形はindexes, indicesがあって、どちらも使う人もいるので、どちらがよく使われるのか調べてみた。
chromiumで調べると、indicesの方が多かった。

- https://source.chromium.org/search?q=%22indexes%22%20filepath:.*%5C.cc$

> Results 1 - 10 of 602

- https://source.chromium.org/search?q=%22indices%22%20filepath:.*%5C.cc$

> Results 1 - 10 of 3,959


```python
class Solution:
    def countComponents(self, n: int, edges: List[List[int]]) -> int:
        adjacency_list = [[] for _ in range(n)]
        visited = set()
        num_connected_component = 0

        for from_, to_ in edges:
            adjacency_list[from_].append(to_)
            adjacency_list[to_].append(from_)

        def search_connected_component(vertex: int) -> None:
            visited.add(vertex)
            for adjacency_vertex in adjacency_list[vertex]:
                if adjacency_vertex in visited:
                    continue
                search_connected_component(adjacency_vertex)
        
        for vertex in range(n):
            if vertex in visited:
                continue
            num_connected_component += 1
            search_connected_component(vertex)

        return num_connected_component
```

## step3
```python
class Solution:
    def countComponents(self, n: int, edges: List[List[int]]) -> int:
        num_connected_component = 0
        visited = set()
        adjacency_list = [[] for _ in range(n)]
        for from_, to_ in edges:
            adjacency_list[from_].append(to_)
            adjacency_list[to_].append(from_)
        
        def search_connected_component(vertex: int) -> None:
            visited.add(vertex)
            for adjacency_vertex in adjacency_list[vertex]:
                if adjacency_vertex in visited:
                    continue
                search_connected_component(adjacency_vertex)

        for vertex in range(n):
            if vertex in visited:
                continue
            num_connected_component += 1
            search_connected_component(vertex)
        
        return num_connected_component
```

