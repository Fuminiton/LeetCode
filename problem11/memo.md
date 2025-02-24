## 取り組み方
- step1: 5分以内に空で書いてAcceptedされるまで解く
- step2: discord内で他の方のコードを5人分読む + 何をよい/悪いと思ったか、何を避けたいと思ったか等感じたことを3つ以上かく
- step3: 10分以内に1回もエラーを出さずに3回連続で解く

## step1
### 考えたこと
numsの要素を一つずつ確認し、既に確認したものをhashテーブルに入れていく。
そのとき、target - nums[x] が既に確認済みであったらhashテーブルからvalueのインデックスを返す。
時間計算量は、hashテーブルに値を入れていく操作で nums、チェックに 1で、10^4程度、10ms程度のオーダーで終わるはず。

```python
class Solution:
    def twoSum(self, nums: List[int], target: int) -> List[int]:
        num_to_index = {}
        for index, num in enumerate(nums):
            needed_num = target - num
            if needed_num in num_to_index:
                return [num_to_index[needed_num], index]
            num_to_index[num] = index
```

## step2
### 読んだコード
- https://github.com/ichika0615/arai60/pull/10/files
- http://github.com/sakupan102/arai60-practice/pull/12/files
- https://github.com/erutako/leetcode/pull/2/files
- https://github.com/Ryotaro25/leetcode_first60/pull/12/files
- https://github.com/kazukiii/leetcode/pull/12/files
- https://github.com/seal-azarashi/leetcode/pull/11/files

### 読んだ感想
- 問題には必ず解が見つかるとあるが、解がみつからなかった場合の挙動も書いておくと丁寧に感じた
  - solutionが見つかるはずなのにないので、与えられたnumsやtargetのValueErrorとする
    - https://docs.python.org/3/library/exceptions.html
- 2つの和の組み合わせを全列挙するパターンは選択肢として見えていたが、target-nums[x]を二分探索で見つけるという選択肢もあったことを知れた。

```python
class Solution:
    def twoSum(self, nums: List[int], target: int) -> List[int]:
        num_to_index = {}
        for index, num in enumerate(nums):
            needed_num = target - num
            if needed_num in num_to_index:
                return [num_to_index[needed_num], index]
            num_to_index[num] = index
        raise ValuerError('Each input would have exactly one solution.')
```

## step3
```python
class Solution:
    def twoSum(self, nums: List[int], target: int) -> List[int]:
        num_to_index = {}
        for index, num in enumerate(nums):
            needed = target - num
            if needed in num_to_index:
                return [num_to_index[needed], index]
            num_to_index[num] = index
        raise ValueError('Solution is not founded.')
```