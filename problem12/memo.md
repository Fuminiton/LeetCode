## 取り組み方
- step1: 5分以内に空で書いてAcceptedされるまで解く
- step2: discord内で他の方のコードを5人分読む + 何をよい/悪いと思ったか、何を避けたいと思ったか等感じたことを3つ以上かく
- step3: 10分以内に1回もエラーを出さずに3回連続で解く

## step1
### 考えたこと
strs[i]のソート結果をキーとし、バリューにstrs[i]を追加し、バリューを返す。
時間計算量は strsの数 * ソートを考慮して、strs.length * strs[i] * log strs[i] = 10^4*10^2*log10^2 で 10^6 程度で問題なさそう。
sorted の結果はミュータブルでキーとして使えないので、連結した文字列に直してからキーとして使う。

```python
class Solution:
    def groupAnagrams(self, strs: List[str]) -> List[List[str]]:
        anagrams = defaultdict(list)
        for str_ in strs:
            sorted_strs = "".join(sorted(str_))
            anagrams[sorted_strs].append(str_)    
        return list(anagrams.values())
```

## step2
### 読んだコード
- https://github.com/TORUS0818/leetcode/pull/14/files
- https://github.com/hayashi-ay/leetcode/pull/19/files
- https://github.com/quinn-sasha/leetcode/pull/12/files
- https://github.com/nittoco/leetcode/pull/13/files
- https://github.com/ryoooooory/LeetCode/pull/1/files
- https://github.com/fhiyo/leetcode/pull/15/files

### 読んだ感想
- イミュータブルなものをキーにすれば良いので、tupleをキーにするのもみえてはいたが、個人的には文字列をキーにした方が直感的なように感じた。
- 文字の度数分布をキーにする選択肢がみえていなかった。
  - 26

```python
class Solution:
    def groupAnagrams(self, strs: List[str]) -> List[List[str]]:
        def get_alphabet_histgram(s: str) -> tuple:
            num_alphabet = 26
            histgram = [0] * num_alphabet
            for c in s:
                histgram[ord(c) - ord('a')] += 1
            return tuple(histgram)

        anagrams = defaultdict(list)
        for s in strs:
            str_histgram = get_alphabet_histgram(s)
            anagrams[str_histgram].append(s)

        return list(anagrams.values())
```

## step3
```python
class Solution:
    def groupAnagrams(self, strs: List[str]) -> List[List[str]]:
        group_anagrams = defaultdict(list)
        for s in strs:
            group_anagrams[tuple(sorted(s))].append(s)
        return list(group_anagrams.values())
```
