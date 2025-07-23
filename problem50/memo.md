## 取り組み方
- step1: 5分以内に空で書いてAcceptedされるまで解く + テストケースと関連する知識を連想してみる
- step2: コードを整える + 他の妥当な実装があれば実装してみる
- step3: 10分以内に1回もエラーを出さずに3回連続で解く

## step1
substringの先頭と末尾の情報と、substringに使われている文字を管理するsetをもつ。文字が重複するまで先頭を進めていき、重複が見つかったら、重複がなくなるまでsetから文字を削除しながら末尾を進めていく。重複が発生していないときは毎回先頭の更新ごとにsubstringの長さを計測すれば、最大長が分かる。

時間計算量は、s.length * setからの削除なので、O(10^4)で数十ミリ秒で終わる見込み。

```py
class Solution:
    def lengthOfLongestSubstring(self, s: str) -> int:
        longest_length = 0
        used = set()
        left = 0

        for right, character in enumerate(s):
            while character in used:
                used.remove(s[left])
                left += 1
            longest_length = max(longest_length, right - left + 1)
            used.add(character)

        return longest_length
```

## step2
### 読んだ
https://github.com/hayashi-ay/leetcode/pull/47/files
https://github.com/TORUS0818/leetcode/pull/50/files
https://github.com/KentaroJay/Leetcode/pull/4/files
https://github.com/ryosuketc/leetcode_arai60/pull/37/files


### 感想
- setを使う派とdictを使う派で分かれるくらいで、自分含め皆さん大体同じようなコードになっていた印象
  - dictの方法は最後に使われたindexを保持するという発想が面白いが、setの方がシンプルでよいと思う
- longest_lengthは変か。longest_substring_len、max_lenくらいの方がよさそう
- pythonの場合は、1文字だからといってASCIIとなる訳ではないそう

> 文字列は Unicode コードポイントのイミュータブルな シーケンス です
> "character" 型が特別に用意されているわけではないので、文字列のインデックス指定を行うと長さ 1 の文字列を作成します。

https://docs.python.org/ja/3.13/library/stdtypes.html#textseq


##### step1の洗練
```py
class Solution:
    def lengthOfLongestSubstring(self, s: str) -> int:
        max_len = 0
        used = set()
        window_start = 0

        for window_end, character in enumerate(s):
            while character in used:
                used.remove(s[window_start])
                window_start += 1
            max_len = max(max_len, window_end - window_start + 1)
            used.add(character)

        return max_len
```

##### 辞書型を使う
```py
class Solution:
    def lengthOfLongestSubstring(self, s: str) -> int:
        def is_last_index_in_window(character: str) -> bool:
            if character not in used_char_to_index:
                return False
            return used_char_to_index[character] >= window_start

        max_len = 0
        used_char_to_index = {}
        window_start = 0

        for window_end, character in enumerate(s):
            if is_last_index_in_window(character):
                window_start = used_char_to_index[character] + 1        
            used_char_to_index[character] = window_end
            max_len = max(max_len, window_end - window_start + 1)
        
        return max_len
```

##### 入力がASCIIと仮定
```py
class Solution:
    def lengthOfLongestSubstring(self, s: str) -> int:
        max_len = 0
        ascii_last_used_index = [-1] * 128
        window_start = 0

        for window_end, character in enumerate(s):
            ascii_value = ord(character)
            if ascii_last_used_index[ascii_value] >= window_start:
                window_start = ascii_last_used_index[ascii_value] + 1
            ascii_last_used_index[ascii_value] = window_end
            max_len = max(max_len, window_end - window_start + 1)
        
        return max_len
```

## step3
```py
class Solution:
    def lengthOfLongestSubstring(self, s: str) -> int:
        max_len = 0
        start = 0
        used = set()

        for end, c in enumerate(s):
            while c in used:
                used.remove(s[start])
                start += 1
            used.add(c)
            max_len = max(max_len, end - start + 1)
        
        return max_len
```