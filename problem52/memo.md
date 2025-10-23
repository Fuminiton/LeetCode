## 取り組み方
- step1: 5分以内に空で書いてAcceptedされるまで解く + テストケースと関連する知識を連想してみる
- step2: コードを整える + 他の妥当な実装があれば実装してみる
- step3: 10分以内に1回もエラーを出さずに3回連続で解く

## step1
[1,2,3]でpermutationsを作ることを考えると、

- [1]
  - [1,2]
    - [1,2,3]
  - [1,3]
    - [1,3,2]
- [2]
  - [2,1]
    - [2,1,3]
  - [2,3]
    - [2,3,1]
- [3]
  - [3,1]
    - [3,1,2]
  - [3,2]
    - [3,2,1]

のようになれば良いので、
ある一つの作りかけのpermutation用の配列に、
1回、値を追加するのを、numsを全て見つつ使われていないものだけが追加されるように、
網羅的に分岐させていくイメージで処理が進めば良い。

リストがミュータブルなので、リストにリストを追加する部分には気をつける。

計算量は、作るpermutationがn!個あって、permutaionsに追加する際にコピーでnかかるので、O(n*n!)。

```py
class Solution:
    def permute(self, nums: List[int]) -> List[List[int]]:
        def create_permutation(used: List[int]) -> List[int]:
            if len(used) == len(nums):
                permutations.append(used[:])
                return 
            for num in nums:
                if num in used:
                    continue
                used.append(num)
                create_permutation(used)
                used.pop()

        permutations = []
        create_permutation([])

        return permutations
```

## step2
### 読んだ
- https://github.com/shining-ai/leetcode/pull/50/files
- https://github.com/tokuhirat/LeetCode/pull/50/files
- https://github.com/python/cpython/blob/main/Modules/itertoolsmodule.c#L2696
- https://github.com/Ryotaro25/leetcode_first60/pull/54/files

### 感想
- これまで使っているnumsの要素が入っているという意味でusedと命名していたが、candidate の方が実体に近いので変えるべき
- itertools.permutationsは読みづらかった。というか理解できていないので、時間をおいて読む
  - とはいえ、再帰を使わない&インデックス操作なので元のデータがどんな型で重複があっても機能するという点で良いと思った
  - が、どのみち n! で時間がかかるので、数値型を扱うならstep1のような形がシンプルで理解しやすいので良いかと思う

#### step1の洗練
使っていないnumsの要素を管理して、再帰の中のループをlen(nums)で回さなくて済むようにする。
ループ中にループの条件に使っている配列に値を追加したり、削除したりするとエラーになるらしいので、気をつける。
https://stackoverflow.com/questions/33679669/python-runtimeerror-dictionary-changed-size-during-iteration/33679686#33679686

ただ、not_usedの浅いコピーを作って回すことになるので、numsでループするのとトータルでどちらが速くなるのかはよくわからない。ので、測ってみる。

```py
class Solution:
    def permute(self, nums: List[int]) -> List[List[int]]:
        def create_permutation(candidate: List[int]) -> List[int]:
            if len(candidate) == len(nums):
                permutations.append(candidate[:])
                return

            for num in list(not_used):
                candidate.append(num)
                not_used.remove(num)
                create_permutation(candidate)
                candidate.pop()
                not_used.add(num)

        permutations = []
        not_used = set(nums)
        create_permutation([])
```

##### step1のコード(numsでループ) vs step1の洗練(not_usedでループ)
nums = list(range(10)) で手元で計測したところ、not_used でループした方が少し早い。

###### step1のコード
1回目: 10.01 seconds
2回目: 9.89 seconds
3回目: 10.08 seconds

###### step1の洗練
1回目: 7.400 seconds
2回目: 7.232 seconds
3回目: 7.284 seconds

#### 再帰からループへの書き換え
1個目の候補を決める、2個目の候補を決める、・・・・と各ステップで何を受け渡して、次のステップに何を引き継げば良いかを考えて書き換える。

```py
class Solution:
    def permute(self, nums: List[int]) -> List[List[int]]:
        permutations = []
        candidates = []
        for num in nums:
            candidates.append([num])

        while candidates:
            candidate = candidates.pop()
            if len(candidate) == len(nums):
                permutations.append(candidate[:])
                continue
            for num in nums:
                if num in candidate:
                    continue
                candidate.append(num)
                candidates.append(candidate[:])
                candidate.pop()

        return permutations
```

#### itertools風
- 後で読む

## step3
```py
class Solution:
    def permute(self, nums: List[int]) -> List[List[int]]:
        permutations = []
        candidates = []
        for num in nums:
            candidates.append([num])
        
        while candidates:
            candidate = candidates.pop()
            if len(candidate) == len(nums):
                permutations.append(candidate)
                continue
            for num in nums:
                if num in candidate:
                    continue
                candidate.append(num)
                candidates.append(candidate[:])
                candidate.pop()

        return permutations
```

