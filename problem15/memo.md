## 取り組み方
- step1: 5分以内に空で書いてAcceptedされるまで解く
- step2: discord内で他の方のコードを5人分読む + 何をよい/悪いと思ったか、何を避けたいと思ったか等感じたことを3つ以上かく
- step3: 10分以内に1回もエラーを出さずに3回連続で解く

## step1
### 考えたこと
#### 処理の流れ
1. defaultdict(int) で文字をキーと出現回数をバリューとする辞書を作成する
2. 文字をはじめから見ていって、出現回数が1のものが見つかったらreturn

ただし、出現回数が1のものがない場合もあることに注意

#### 計算量
- 辞書の作成に len(s)、文字をはじめから見ていくのに、len(s) 分で大体、10^5なので1秒以内で余裕で終わりそう

```python
class Solution:
    def firstUniqChar(self, s: str) -> int:
        char_to_count = defaultdict(int)
        for c in s:
            char_to_count[c] += 1
        for index, c in enumerate(s):
            if char_to_count[c] == 1:
                return index
        return -1
```

## step2
### 読んだコード
- https://github.com/su33331/practice/pull/2/files
- https://github.com/hayashi-ay/leetcode/pull/28/files
- https://github.com/hroc135/leetcode/pull/15/files
- https://github.com/rihib/leetcode/pull/18/files
- https://github.com/rihib/leetcode/pull/18/files

### 読んだ感想
- 辞書を作らずに、現在の文字より後ろにある
- 空文字が入ってきた場合について、辞書を生成する時間も惜しいと考えるならば早期リターンすべきであることに気づけた。
- 「for c in s」の「c」は letter など良いかと持ったが、「c」でも許容ラインっぽい。
- count は frequency という命名の方が自然に感じた
- LeetCode をやり始めてから Counter を知ったのに、使う選択肢が見えなかった点が悔やまれる。

```python
class Solution:
    def firstUniqChar(self, s: str) -> int:
        if s == '':
            return -1
        char_to_frequency = Counter(s)
        for index, c in enumerate(s):
            if char_to_frequency[c] == 1:
                return index
        return -1
```

## step3
```python
class Solution:
    def firstUniqChar(self, s: str) -> int:
        char_to_frequency = Counter(s)
        for index, c in enumerate(s):
            if char_to_frequency[c] == 1:
                return index
        return -1
```

### 感想
