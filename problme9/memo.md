## 取り組み方
- step1: 5分以内に空で書いてAcceptedされるまで解く
- step2: discord内で他の方のコードを5人分読む + 何をよい/悪いと思ったか、何を避けたいと思ったか等感じたことを3つ以上かく
- step3: 10分以内に1回もエラーを出さずに3回連続で解く

## step1
### 考えたこと
- numsをkey、頻度をvalueとした辞書型配列を用意する
- 頻度をソートキーにして降順ソートする
- ソートした結果のkeyを配列に入れてk番目までを返す
- 時間計算量は、ソートする部分が最大で nums.length * log(nums.length)
  - 10^5 * log 10^5 で 10^6 くらいなので許容か
    - 時間計算量がどれくらいなら許容なのかの基準を調べる
- 空間計算量は、辞書型配列のソートのための一時領域を確保する時が最大で、keyとvalue分の領域と一時領域を考慮すると (nums.length*2)*2
  - 4*10^5くらいで許容？
    - 空間計算量もどれくらいなら許容なのかの基準は調べる
- ソートキーの指定の仕方がわからず調べた
  - key=lambda x: x[1] で value をソート基準にする」 

### 読んだソース
- https://docs.python.org/3.13/library/collections.html#collections.defaultdict
- https://docs.python.org/3.13/library/functions.html#sorted

```python
class Solution:
    def topKFrequent(self, nums: List[int], k: int) -> List[int]:
        frequency = defaultdict(int)
        for num in nums:
            frequency[num] += 1
        sorted_frequency = dict(sorted(frequency.items(), key=lambda x: x[1], reverse=True))
        return list(sorted_frequency.keys())[:k]
```

## step2
### 読んだコード
- https://github.com/tarinaihitori/leetcode/pull/9/files#diff-013dde8937de27960afd2d09c6325ebf53eb00c65d9d2772bc598e798bcdb877
- https://github.com/hayashi-ay/leetcode/pull/60/files
- https://github.com/kazukiii/leetcode/pull/10/files
- https://github.com/kagetora0924/leetcode-grind/pull/11/files
- https://github.com/katataku/leetcode/pull/9/files?short_path=013dde8#diff-013dde8937de27960afd2d09c6325ebf53eb00c65d9d2772bc598e798bcdb877

### 読んだソース
- https://docs.python.org/3/library/collections.html#collections.Counter

### 読んだ感想
- ヒープとソートを使う2つの選択肢しか自分は見えていなかったが、バケットソートやクイックセレクトといった選択肢も考えている人もいて良いなと思った
- mapやdictの命名として、XX_to_YYという命名は一眼で何をするものか分かるのでいい命名だと感じた
- sortedのキーにlambdaを用いて書くのは不親切で、itemgetterやvalueを返すカスタム関数などを定義した方が丁寧なのかなと感じた

```python
class Solution:
    def topKFrequent(self, nums: List[int], k: int) -> List[int]:
        def get_value(item) -> int:
            return item[1]

        num_to_freqency = Counter()
        for num in nums:
            num_to_freqency[num] += 1
        sorted_num_to_freqency = dict(sorted(
            num_to_freqency.items(),
            key=get_value,
            reverse=True))
        return list(sorted_num_to_freqency.keys())[:k]
```

## step3
```python
class Solution:
    def topKFrequent(self, nums: List[int], k: int) -> List[int]:
        num_to_count = Counter()
        for num in nums:
            num_to_count[num] += 1
        count_to_num_heap = []
        for num, count in num_to_count.items():
            heapq.heappush(count_to_num_heap, (count, num))
            if len(count_to_num_heap) > k:
                heapq.heappop(count_to_num_heap)
        return [num for _, num in count_to_num_heap]
```

### 取り組み全体を通しての感想
- ソートよりヒープで解いた方が、dictやlistなどの型変換がないので、コードを直しやすいのかなと思い、ヒープを最終的なコードとした。
