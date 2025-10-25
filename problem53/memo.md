## 取り組み方
- step1: 5分以内に空で書いてAcceptedされるまで解く + テストケースと関連する知識を連想してみる
- step2: コードを整える + 他の妥当な実装があれば実装してみる
- step3: 10分以内に1回もエラーを出さずに3回連続で解く

## step1
bitを使ってループすれば使うor使わないのパターンを表現できるが、樹形図で考えてみる。

numsについて、使うor使わないの組み合わせを列挙して、最後にsubsetsを構築していけば良い。使うor使わないの組み合わせを列挙するために、0番目からlen(nums)-1番目までをコイントスの裏表の分岐のようになるように再帰をかけば良い。

1度の再帰で分岐が使うor使わないの分岐で2通り、再帰の回数がlen(nums)回、選び終わったタイミングでsubsetを構築するのにlen(nums)回必要なので、`2 ^ len(nums) * len(nums)`の時間がかかる。

```py
class Solution:
    def subsets(self, nums: List[int]) -> List[List[int]]:
        def create_subset() -> List[int]:
            subset = []
            for i in range(len(nums)):
                if not is_selected[i]:
                    continue
                subset.append(nums[i])
            return subset
        
        def select_index(i: int) -> None:
            if i == len(nums):
                subset = create_subset()
                subsets_list.append(subset)
                return
            is_selected[i] = False
            select_index(i + 1)
            is_selected[i] = True
            select_index(i + 1)

        subsets_list = []
        is_selected = [False] * len(nums)
        select_index(0)

        return subsets_list
```

## step2
### 読んだコードと感想
- https://github.com/hayashi-ay/leetcode/pull/63/files#diff-ddd8c09ee41837c8d5bde978403f850a0b08217fb8ec8eac6d0f2ae10e369d04R7
  - appendとpopを駆使してsubsetを作りながら進める方針を使用している
    - indexを0から選ぶか選ばないかでループするという発想自体は自分と同じだが、こちらの方法の方はcreate_subsetsの部分を作らなくて良く、シンプルで良い
- https://github.com/hayashi-ay/leetcode/pull/63/files#diff-ddd8c09ee41837c8d5bde978403f850a0b08217fb8ec8eac6d0f2ae10e369d04R60
  - bitmaskも分かりやすい
  - が、bitmaskを使って、選ぶor選ばないみたいな使い方をするのは常識なのか？
- https://github.com/olsen-blue/Arai60/pull/52/files#r2019033451
  - 面白い
  - 言われてみればという感じだが、i番目までで作れる集合がわかっていれば、i+1番目はそれらの集合それぞれに対して、nums[i+1]を使うか、使わないかでi+1番目まででできる部分集合を全て作れる
- https://github.com/fhiyo/leetcode/pull/51/files#diff-32ae6e97704fc6ebe760617c9b69078b525330c93c332d2e1bac0ce35dc11f4bR16
  - 最後に部分集合の一覧を返さないといけないので今回はあまり適さないと思う
  - が、自分もyield, yield fromを使う選択肢は見えても良かったかも

### その他感想
- subsetsという関数名は微妙では？
  - returnで返したい変数に使えないので面倒
- 


### 1. 使う使わないの分岐を走査する(step1の洗練)
```py
class Solution:
    def subsets(self, nums: List[int]) -> List[List[int]]:
        def get_subset(index: int) -> None:
            if index == len(nums):
                all_subsets.append(subset[:])
                return
            subset.append(nums[index])
            get_subset(index + 1)
            subset.pop()
            get_subset(index + 1)
        
        subset = []
        all_subsets = []
        get_subset(0)

        return all_subsets
```

### 2. bitmaskを使って01で使う使わないを表現
例えば、nums=[1,2,3]の時、000,001,010,....,110,111を考えていく。
110なら[2,3]。※最下位bitから数えていくので、[1,2]にならない。

```py
class Solution:
    def subsets(self, nums: List[int]) -> List[List[int]]:
        def create_subset_from_bitmask(bitmask: int) -> List[List[int]]:
            subset = []
            index = 0
            while bitmask:
                if bitmask & 1:
                    subset.append(nums[index])
                bitmask >>= 1
                index += 1
            return subset

        all_subsets = []
        for i in range(2**len(nums)):
            subset = create_subset_from_bitmask(i)
            all_subsets.append(subset)
        
        return all_subsets
```


### 3. i番までで作れる集合の集合Xが与えられた時に、i+1番までを使ってできる集合は、集合Xにi+1番の要素を入れるか入れないかである性質を使う
例えば、nums=[1,2,3]の時、1番目までを使えば、
[], [1], [2], [1,2] が候補になるが、
これを前提に2番目の要素を考えていくと、
元の候補に3を加えるか、加えないかのパターンを網羅できれば良いから

[]       -> [], [3]
[1]      -> [1], [1,3]
[2]      -> [2], [2,3]
[1,2]    -> [1,2], [1,2,3]

のような形で2番目の要素を全て得られる。

```py
class Solution:
    def subsets(self, nums: List[int]) -> List[List[int]]:
        def add_num_to_subsets(num: int, target_subsets: List[List[int]]) -> List[List[int]]:
            added_subsets = []
            for subset in target_subsets:
                added_subset = subset + [num]
                added_subsets.append(added_subset)
            return added_subsets

        all_subsets = [[]]
        for num in nums:
            added = add_num_to_subsets(num, all_subsets)
            all_subsets += added

        return all_subsets
```

## step3

```py
class Solution:
    def subsets(self, nums: List[int]) -> List[List[int]]:
        def create_subset_from_bitmask(bitmask: int) -> List[List[int]]:
            subset = []
            index = 0
            while bitmask:
                if bitmask & 1:
                    subset.append(nums[index])
                bitmask >>= 1
                index += 1
            return subset
        
        all_subsets = []
        for i in range(2**len(nums)):
            subset = create_subset_from_bitmask(i)
            all_subsets.append(subset)
        return all_subsets
```