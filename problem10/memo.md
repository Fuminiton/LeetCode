## 取り組み方
- step1: 5分以内に空で書いてAcceptedされるまで解く
- step2: discord内で他の方のコードを5人分読む + 何をよい/悪いと思ったか、何を避けたいと思ったか等感じたことを3つ以上かく
- step3: 10分以内に1回もエラーを出さずに3回連続で解く

## step1
### 考えたこと
nums1 と nums2 の組み合わせを全部作って最小をだすのは、10^5 * 10^5 でPythonが 1 秒間あたりに処理できるステップ数の目安の 10^6 を超え、処理に約3時間近くかかるものが正しいとされることはなさそう。問題文に特に時間制約は書かれていないが、流石に長いと思うので別の方法を考える。

nums1,2 はソートずみなので、nums1[0]+nums2[0] は当然、最小値であるはず。
次の最小値をとり得るのは、nums1[1]+nums2[0] or nums1[0]+nums2[1]。
次の次の最小値をとり得るのは、
前回がnums1[i]+nums2[j]であったとすれば、nums1[i+1]+nums2[j] or nums1[i]+nums2[j+1]。
この操作のk回目で最小値をとり得るのは、k-1回目までに出てきたnums1とnums2の要素の和でまだpopされていないもの or (前回がnums1[ki]+nums2[kj]として)nums1[ki+1]+nums2[kj] or nums1[ki]+nums2[kj+1]。

と考えると、この和の組み合わせを最小ヒープと既にヒープに登録した組み合わせであるかを管理しつつ、最小値となる組み合わせをpopしていけばよい。

時間計算量は、「既にヒープに挿入したかの判定, ヒープへの挿入, 最小ヒープのpopの中の最大時間がかかるもの」*「k回の操作」で、計算できるので、大体、O(k*logk)で 10^4 * 4 * 3.3 -> 10^5 で 1 秒以内で終わりそうで、これならTLEにはならなそう。
空間計算量は、すでにpushした組み合わせを格納する配列, 和の組み合わせを格納するヒープ、結果を格納するタプルの最大を考慮して、O(min(k, nums1.length*nums2.length)) -> 10^4程度で、余裕でメモリに乗り切りそう。

```python
class Solution:
    def kSmallestPairs(self, nums1: List[int], nums2: List[int], k: int) -> List[List[int]]:
        visited = set()
        sum_and_index_pairs = []
        nums_pairs = []

        heapq.heappush(sum_and_index_pairs, (nums1[0] + nums2[0], 0, 0))
        visited.add((0, 0))
        while len(nums_pairs) < k and sum_and_index_pairs:
            _, nums1_i, nums2_i = heapq.heappop(sum_and_index_pairs)
            nums_pairs.append([nums1[nums1_i], nums2[nums2_i]])

            if nums1_i+1 < len(nums1) and (nums1_i+1, nums2_i) not in visited:
                visited.add((nums1_i+1, nums2_i))
                heapq.heappush(
                    sum_and_index_pairs,
                    (nums1[nums1_i+1]+nums2[nums2_i], nums1_i+1, nums2_i)
                    )
            if nums2_i+1 < len(nums2) and (nums1_i, nums2_i+1) not in visited:
                visited.add((nums1_i, nums2_i+1))
                heapq.heappush(
                    sum_and_index_pairs,
                    (nums1[nums1_i]+nums2[nums2_i+1], nums1_i, nums2_i+1)
                    )
        return nums_pairs
```

## step2
### 読んだコード
- https://github.com/nittoco/leetcode/pull/33
- https://github.com/colorbox/leetcode/pull/25
- https://github.com/kazukiii/leetcode/pull/11
- https://github.com/Ryotaro25/leetcode_first60/pull/11
- https://github.com/fhiyo/leetcode/pull/13
- https://github.com/Mike0121/LeetCode/pull/20

### 読んだ感想
pushは同じ処理をしているので関数化した方が良さそう。
返り値は k_smallest_sum_pairs の方が適切な命名に感じた。
nums1, nums2 のインデックスを i,j としたときに、
nums1[i]+nums2[j+1]を追加し、j=0の時だけ nums1[i+1]+nums2[0] も追加する規則性を持たせると重複した要素を考えることがなくなる。
一方、自分は重複した列も考慮した上で探索していく方が直感的に理解しやすいと思った。


```python
class Solution:
    def kSmallestPairs(self, nums1: List[int], nums2: List[int], k: int) -> List[List[int]]:
        visited = set()
        sum_and_index_pairs = []
        k_smallest_sum_pairs = []

        heapq.heappush(sum_and_index_pairs, (nums1[0] + nums2[0], 0, 0))
        visited.add((0, 0))

        def add_candidates(i: int, j: int) -> None:
            if i < len(nums1) and j < len(nums2) and (i, j) not in visited:
                visited.add((i, j))
                heapq.heappush(
                    sum_and_index_pairs,
                    (nums1[i] + nums2[j], i, j)
                    )

        while len(k_smallest_sum_pairs) < k and sum_and_index_pairs:
            _, index1, index2 = heapq.heappop(sum_and_index_pairs)
            k_smallest_sum_pairs.append([nums1[index1], nums2[index2]])
            add_candidates(index1 + 1, index2)
            add_candidates(index1, index2 + 1)

        return k_smallest_sum_pairs
```

## step3
```python
class Solution:
    def kSmallestPairs(self, nums1: List[int], nums2: List[int], k: int) -> List[List[int]]:
        visited = set()
        k_smallest_pairs = []
        candidates = []

        visited.add((0, 0))
        heapq.heappush(
            candidates,
            (nums1[0]+nums2[0], 0, 0)
            )
        
        def add_candidates(i: int, j: int) -> None:
            if len(nums1) <= i or len(nums2) <= j or (i, j) in visited:
                return None
            visited.add((i, j))
            heapq.heappush(
                candidates,
                (nums1[i]+nums2[j], i, j)
            )
        
        while len(k_smallest_pairs) < k:
            _, index1, index2 = heapq.heappop(candidates)
            k_smallest_pairs.append([nums1[index1], nums2[index2]])
            add_candidates(index1 + 1, index2)
            add_candidates(index1, index2 + 1)
        
        return k_smallest_pairs
```
