## 取り組み方
- step1: 5分以内に空で書いてAcceptedされるまで解く
- step2: discord内で他の方のコードを5人分読む + 何をよい/悪いと思ったか、何を避けたいと思ったか等感じたことを3つ以上かく
- step3: 10分以内に1回もエラーを出さずに3回連続で解く

## step1
### 考えたこと
ある文字列から1文字変化した結果は、(小文字アルファベットの数-1)*(beginWordの文字を変える位置)分。
1文字変化した文字列が未訪問 or wordListに存在していれば1文字変化した先として認める。
この流れをbeginWordから繰り返していき、現在見ている文字列がendWordになったら終了。

最小パスを答える必要があるので、訪問リストを遷移先の文字列ととして持ち。
ある文字列からの遷移先を訪問リストに追加し終わったタイミングでbeginWordから変えた文字数をインクリメントする形で、BFSで実装する。

時間計算量は、wordListを辞書に登録すれば、
全ての辺の数分になり、(文字列の遷移先)^2*文字数=(25*10)^2*10=125*10^3~10^5
空間計算量は、(文字列の遷移先)^2

### 感想
上下左右や隣接する頂点といった移動に関するdfsやbfsをしてきたが、文字列の変化にも使っていけるのかと少し感動した。

```python
class Solution:
    def ladderLength(self, beginWord: str, endWord: str, wordList: List[str]) -> int:
        word_set = set(wordList)
        if not endWord in word_set:
            return 0

        visited = set([beginWord])
        visiting = deque([(beginWord, 1)])
        while visiting:
            current_word, step = visiting.popleft()
            if current_word == endWord:
                return step
            for i in range(len(beginWord)):
                for letter in string.ascii_lowercase:
                    new_word = f'{current_word[:i]}{letter}{current_word[i+1:]}'
                    if new_word in visited:
                        continue
                    if not new_word in word_set:
                        continue
                    visited.add(new_word)
                    visiting.append((new_word, step + 1))
        return 0
```

## step2
### 読んだコード
- https://github.com/Yusan1234/arai60/pull/1/files
- https://github.com/hroc135/leetcode/pull/19/files
- https://github.com/seal-azarashi/leetcode/pull/19/files
- https://github.com/Ryotaro25/leetcode_first60/pull/22/files
- https://github.com/hayashi-ay/leetcode/pull/42/files
- https://github.com/fhiyo/leetcode/pull/22#discussion_r1641678912
- 

### 読んだ感想
- はじめは、visiting に文字列だけを入れていて、途中で変更回数も格納するようにしたため、微妙な変数名になってしまったことに解いた後から気づいた。

- 2回for文を回すところは、1文字変えた文字列を生成することが目的なので、関数化してシンプルに書き直したい。
  - Generator で書いている人がいたので真似する。
  - 型ヒントの書き方は知らなかったので調べた。

>  Generator[YieldType, SendType, ReturnType]
https://docs.python.org/3.13/library/typing.html

- for文を使う際に一瞬、1文字で良いのか迷う時があるので、20行くらい続く処理かどうかは基準として良さそう。

> 一文字にするのは「ループを出たら忘れ去られていいような変数」ならばいいですが、20行も覚えている必要があるやつには使わないで欲しいですね。
https://github.com/Yusan1234/arai60/pull/1/files#r1841958098

- 月並みだが、java や go は関数、変数名が長いなあと感じた。

- visited ではなく、unused を使っていけば、まだ使っていない文字列とword_listに含まれる文字列を同時に管理できて良さそう。

```python
class Solution:
    def ladderLength(self, begin_word: str, end_word: str, word_list: List[str]) -> int:
        unused = set(word_list)
        if not end_word in unused:
            return 0
        words = [begin_word]
        num_transformation = 1

        def generate_one_letter_diff_words(word: str) -> Generator[str, None, None]:
            for i in range(len(word)):
                for letter in string.ascii_lowercase:
                    new_word = f'{word[:i]}{letter}{word[i+1:]}'
                    if not new_word in unused:
                        continue
                    unused.remove(new_word)
                    yield new_word

        while words:
            next_words = []
            for word in words:
                if word == end_word:
                    return num_transformation
                for next_word in generate_one_letter_diff_words(word):
                    next_words.append(next_word)
            num_transformation += 1
            words = next_words

        return 0
```

## step3
### 感想
- 途中から、unusedより、candidatesの方が理解しやすいなと感じた

```python
class Solution:
    def ladderLength(self, begin_word: str, end_word: str, word_list: List[str]) -> int:
        candidates = set(word_list)
        if not end_word in candidates:
            return 0
        num_transformation = 1
        words = [begin_word]

        def generate_adjacency_words(word: str) -> Generator[str, None, None]:
            for i in range(len(word)):
                for letter in string.ascii_lowercase:
                    adjacency_word = f'{word[:i]}{letter}{word[i+1:]}'
                    if not adjacency_word in candidates:
                        continue
                    candidates.remove(adjacency_word)
                    yield adjacency_word

        while words:
            next_words = []
            for word in words:
                if word == end_word:
                    return num_transformation
                for next_word in generate_adjacency_words(word):
                    next_words.append(next_word)
            num_transformation += 1
            words = next_words
        return 0
```

