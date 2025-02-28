## 取り組み方
- step1: 5分以内に空で書いてAcceptedされるまで解く
- step2: discord内で他の方のコードを5人分読む + 何をよい/悪いと思ったか、何を避けたいと思ったか等感じたことを3つ以上かく
- step3: 10分以内に1回もエラーを出さずに3回連続で解く

## step1
### 考えたこと
nums1、nums2 をそれぞれソートした後に、
- 要素が一致したら結果setに格納してそれぞれを次の値に進める
- 一致しなかったら辞書順で大きい方を進める
といったように進めていく。
時間計算量は、nums.lengthをNとして、ソートに NlogN、N+Nなので、O(NlogN) 程度で問題なさそう。空間計算量は、O(N)で問題なし。

もしくは、nums2の要素をsetに入れて、nums1の要素がsetにあるかないかを判定していく流れでも、setの検索 logN * 要素分のチェック N -> O(NlogN) で解けるはず。

```python
class Solution:
    def intersection(self, nums1: List[int], nums2: List[int]) -> List[int]:
        index1, index2 = 0, 0
        sorted_nums1, sorted_nums2 = sorted(nums1), sorted(nums2)        
        intersection = set()

        while True:
            if len(sorted_nums1) <= index1 or len(sorted_nums2) <= index2:
                break
            elif sorted_nums1[index1] == sorted_nums2[index2]:
                intersection.add(sorted_nums1[index1])
                index1 += 1
                index2 += 1
            elif sorted_nums1[index1] < sorted_nums2[index2]:
                index1 += 1
            else:
                index2 += 1
        return list(intersection)
```
```python
class Solution:
    def intersection(self, nums1: List[int], nums2: List[int]) -> List[int]:
        intersection = set()
        nums2_set = set(nums2)
        
        for num1 in nums1:
            if num1 in nums2_set:
                intersection.add(num1)
        return list(intersection)
```

## step2
### 読んだコード
- https://github.com/Mike0121/LeetCode/pull/30/files
- https://github.com/nittoco/leetcode/pull/15/files
- https://github.com/fhiyo/leetcode/pull/16/files
- https://github.com/sakupan102/arai60-practice/pull/14/files
- https://github.com/quinn-sasha/leetcode/pull/13/files

### 読んだ感想
- setで積集合が取れることを知れた。なんと1行で解けてしまう。。。
- setを使っているので問題にはならないが、同じ数字がnums1に含まれるときに、2回intersectionにaddする操作が入るのは何かしら考慮した方が良さそう。
- 2つ目の解法は、nums1,2の小さい方の要素でループしてあげた方が早いはず
  - X < Y の時、XlogY < YlogX となることを示せば良い
  - f(T)=log(T)/T を微分すると、(1-log(T))/T^2
  - T>2で単調減少になることがわかるから、X>2 の時 f(Y) < f(X)
  - とはいえ、Y側を使って比較用のsetを初期化する工程があるので、時間を計測して比較することになるのだろう
- そもそもsetのメソッドにintersectionがあるので、intersectionという命名は不適切。
  - https://docs.python.org/3/library/stdtypes.html#frozenset.intersection

```python
class Solution:
    def intersection(self, nums1: List[int], nums2: List[int]) -> List[int]:
        intersection_ = []
        larger = set(nums1)
        smaller = set(nums2)
        if len(larger) < len(smaller):
            larger, smaller = smaller, larger

        for num in smaller:
            if num in larger:
                intersection_.append(num)
        return intersection_
```

## step3
```python
class Solution:
    def intersection(self, nums1: List[int], nums2: List[int]) -> List[int]:
        return list(set(nums1).intersection(set(nums2)))
```

### 感想
- 単純な問題かと思ったが、さまざまな議論があってdiscordを漁っていて面白かった。
- どれくらい空間計算量がかかると許容されないのかの基準をあまりよく分かっていないので調べる。実装方針の比較基準として持っておきたいと思ったため。