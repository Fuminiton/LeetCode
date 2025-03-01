## 取り組み方
- step1: 5分以内に空で書いてAcceptedされるまで解く
- step2: discord内で他の方のコードを5人分読む + 何をよい/悪いと思ったか、何を避けたいと思ったか等感じたことを3つ以上かく
- step3: 10分以内に1回もエラーを出さずに3回連続で解く

## step1
### 考えたこと
#### 処理の流れ
1. "@"前後で文字列を分解する
2. local name は初めから見ていって初めて出現した "+" の前後で分ける
3. 2.の中に含まれる "." を取り除く
4. 1. でとった domain と 3. と "@" をつなげる
5. 4. を set に追加する
6. 1.~5. の操作を与えられたメールアドレスの数だけやって、最後に set のサイズを返す

#### 計算量
1.~5. の操作で文字列の長さ分の時間がかかり、それをemailの数だけやるので、10^2*10^2で時間的には問題なさそう。

```python
class Solution:
    def numUniqueEmails(self, emails: List[str]) -> int:
        def get_destination(email: str) -> str:
            local, domain = email.split('@')
            local = local.split('+', 1)[0]
            local = local.replace('.', '')
            destination = local + '@' + domain
            return destination

        destinations = set()
        for email in emails:
            destination = get_destination(email)
            destinations.add(destination)
        return len(destinations)
```

## step2
### 読んだコード
- https://github.com/quinn-sasha/leetcode/pull/14/files
- https://github.com/sakupan102/arai60-practice/pull/15
- https://github.com/nittoco/leetcode/pull/7/files
- https://github.com/fhiyo/leetcode/pull/17/files
- https://github.com/Ryotaro25/leetcode_first60/pull/15/files
- https://github.com/goto-untrapped/Arai60/pull/8/files

### 読んだ感想
- localにおいて「"」で囲まれた '@' はある可能性があるので、たまたまテストは通ってしまっているが、自分のコードはアウト。
  - email を右から見ていってsplitすべき
- f-string を用いて文字列を組み立てた方が見やすい
- 個人的には、正規表現の解法は解読するのが面倒だなと思ってしまった。
- java の文字列操作は python と似たような書き方ができるのが発見だった。

```python
class Solution:
    def numUniqueEmails(self, emails: List[str]) -> int:
        def get_destination(email: str) -> str:
            local, domain = email.rsplit('@', 1)
            local = local.split('+', 1)[0]
            local = local.replace('.', '')
            return f'{local}@{domain}'
        
        destinations = set(get_destination(email) for email in emails)
        return len(destinations)
```

## step3
```python
class Solution:
    def numUniqueEmails(self, emails: List[str]) -> int:
        def get_destination(email: str) -> str:
            local, domain = email.rsplit('@', 1)
            local = local.split('+', 1)[0]
            local = local.replace('.', '')
            return f'{local}@{domain}'
        
        destinations = set()
        for email in emails:
            destinations.add(get_destination(email))
        return len(destinations)
```

### 感想
- 内包表記より、forで回した方がやはりわかりやすいか。