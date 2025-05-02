## 取り組み方
- step1: 5分以内に空で書いてAcceptedされるまで解く + テストケースと関連する知識を連想してみる
- step2: 他の方の記録を読んで連想すべき知識や実装を把握した上で、前提を置いた状態で最適な手法を選択し実装する
- step3: 10分以内に1回もエラーを出さずに3回連続で解く

## step1
1-indexで、is_creatable[i]をi番目までの文字sが辞書の文字で構成できるかを表す真偽値の配列とする。

`s`を1文字目から見て行った時に、
`is_creatable[i-len(word)]`が`True`で、かつ、
`s[i-len(word):i]`と`word`が一致すれば、`is_creatable[i]`を`True`とするように更新していく。

計算量は、

- 開始位置を`0~len(s)-1`までループするのに`O(len(s))`
- 毎回、`wordDict`を見るのに`O(len(wordDict))`
- `wordDict`と比較するための文字列をスライスするのに`O(len(s))`

で、`O(len(s)^2 * len(wordDict))`となり、大体`300*300*20 ~ 10^6`程度で問題なさそう。

かなり、応用範囲が広そうな問題かと思った。

```python
class Solution:
    def wordBreak(self, s: str, wordDict: List[str]) -> bool:
        if not s:
            return False
        is_creatable = [False] * (len(s) + 1)  # 1-index
        is_creatable[0] = True
        for index in range(1, len(s) + 1):
            for word in wordDict:
                word_start_index = index - len(word)
                if not 0 <= word_start_index < len(s):
                    continue
                if not is_creatable[word_start_index]:
                    continue
                if s[word_start_index:index] == word:
                    is_creatable[index] = True
        return is_creatable[len(s)]
```

## step2
### 読んだコード
- https://github.com/Mike0121/LeetCode/pull/52/files
- https://github.com/fuga-98/arai60/pull/39
- https://github.com/hayashi-ay/leetcode/pull/61/files
- https://github.com/goto-untrapped/Arai60/pull/20/files

### 感想
- `is_creatable[index] = True`が終わったら、`break`して良い

- DFS, BFSで解くパターンもあった
  - また、「構築できる方法が何パターンあるかも教えてほしい」という要望にも耐えうる点も良い

- 単語の一致の判定に`startswith`が使える
  - DFS, BFSの解法にどこまでが一致しているかのインデックス情報を与えられさえすれば、スライスを避けられる
    - 計算量、予測される今後の変更、追加要件を考えると、BFSでインデックス情報だけをもつやり方が良さそう

> 文字列が指定された prefix で始まるなら True を、そうでなければ False を返します。

https://docs.python.org/ja/3.13/library/stdtypes.html#str.startswith

- そのほかにも、ローリングハッシュ、Trieを使う実装があったが、後で理解する

- 辞書の単語が増えていった場合、wordDictに`set`を使うのが良さそう


#### step1の改良
- is_creatableをis_substring_creatableに
- 1-indexでsのi番目まで構築できることが分かったらbreakする
- startswithでスライシングを回避

```python
class Solution:
    def wordBreak(self, s: str, wordDict: List[str]) -> bool:
        if not s:
            return False
        is_substring_creatable = [False] * (len(s) + 1)  # 1-index
        is_substring_creatable[0] = True

        for i in range(1, len(s) + 1):
            for word in wordDict:
                if not 0 <= i - len(word) < len(s):
                    continue
                if not is_substring_creatable[i - len(word)]:
                    continue
                if s.startswith(word, i - len(word)):
                    is_substring_creatable[i] = True
                    break
        return is_substring_creatable[-1]
```

#### BFS
```python
class Solution:
    def wordBreak(self, s: str, wordDict: List[str]) -> bool:
        if not s:
            return False
        indices = [0]
        visited = set()

        def get_next_indices(start_index: int) -> List[int]:
            indices = []
            for word in wordDict:
                if not s.startswith(word, start_index):
                    continue
                indices.append(start_index + len(word))
            return indices

        while indices:
            next_indices = []
            for index in indices:
                if index == len(s):
                    return True
                if index in visited:
                    continue
                visited.add(index)
                next_indices += get_next_indices(index)
            indices = next_indices

        return False
```

## step3
```python
class Solution:
    def wordBreak(self, s: str, wordDict: List[str]) -> bool:
        if not s:
            return False

        def get_next_indices(start_index: int) -> List[int]:
            next_indices = []
            for word in wordDict:
                if not s.startswith(word, start_index):
                    continue
                next_indices.append(start_index + len(word))
            return next_indices

        indices = [0]
        visited = set()
        while indices:
            next_indices = []
            for index in indices:
                if index == len(s):
                    return True
                if index in visited:
                    continue
                visited.add(index)
                next_indices += get_next_indices(index)
            indices = next_indices
        return False
```
