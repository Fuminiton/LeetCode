## 取り組み方
- step1: 5分以内に空で書いてAcceptedされるまで解く
- step2: discordとwebで検索した結果を踏まえ、コードの洗練を洗練させる + 何を悪いと思ったか、何を避けたいと思ったか等感じたことを記載する
- step3: 10分以内に1回もエラーを出さずに3回連続で解く
- step4: binary heapを実装して解く

## step1
```python
class KthLargest:
    def __init__(self, k: int, nums: List[int]):
        self.k = k
        self.from_top_to_k = nums
        heapq.heapify(self.from_top_to_k)

    def add(self, val: int) -> int:
        heapq.heappush(self.from_top_to_k, val)
        while len(self.from_top_to_k) > self.k:
            heapq.heappop(self.from_top_to_k)
        return self.from_top_to_k[0]
```
### 思考過程
step1: heapqを用いてスコアのをヒープを管理する
step2: 要素の数をk個まで減らせば最小ヒープ = k番目に大きい数になる

- 値を負にして格納する方針もあるが、ミスりそう

N を nums.length として、
時間計算量は 
- valの挿入: O(logN)
- nums.length - k 回の削除: O(NlogN)
- 最小ヒープの取得: O(1)
となるので、O(NlogN)。
空間計算量はヒープの使用でO(N)。

### 感想
- numsのデータを削除しないで取っておいて、別のヒープを作った方がスコアの一覧をみたいとなった場合も考慮できて丁寧なのかもしれない

## step2
```python
class KthLargest:
    def __init__(self, k: int, nums: List[int]):
        self.k = k
        self.top_k = nums
        heapq.heapify(self.top_k)      

    def add(self, val: int) -> int:
        heapq.heappush(self.top_k, val)
        while len(self.top_k) > self.k:
            heapq.heappop(self.top_k)
        return self.top_k[0]
```
### 感想
- たまたまnumsが空で来た時に対処できるコードになっていただけで、step1でnumsが空のケースを意識できていなかったことに反省した
  - heappushをheappushpopに使用としたことで気づいた

## step3
```python
class KthLargest:
    def __init__(self, k: int, nums: List[int]):
        self.k = k
        self.top_k = nums
        heapq.heapify(self.top_k)      

    def add(self, val: int) -> int:
        heapq.heappush(self.top_k, val)
        while len(self.top_k) > self.k:
            heapq.heappop(self.top_k)
        return self.top_k[0]
```
### 感想
- すんなり解けたので、次は binary heap を実装してみる。
