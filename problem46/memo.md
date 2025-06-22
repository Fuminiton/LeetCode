## 取り組み方
- step1: 5分以内に空で書いてAcceptedされるまで解く + テストケースと関連する知識を連想してみる
- step2: 他の方の記録を読んで連想すべき知識や実装を把握した上で、前提を置いた状態で最適な手法を選択し実装する
- step3: 10分以内に1回もエラーを出さずに3回連続で解く

## step1
0
0 1
01 10
0110 1001
01101001 10010110

上記のように、前半の反転が後半になることを利用して、1個になるまで範囲を狭めていく。この時、kの位置は、前半にあればそのままで、後半にあればk-全体の半分のサイズにあたる位置に割り当てて次のループに渡す。

n回再帰するので、O(n)

```py
class Solution:
    def kthGrammar(self, n: int, k: int) -> int:
        def caluclate_kth_symbol(n: int, k: int) -> int:
            if n == 1:
                return 0
            half_rows = 2 ** (n - 2)
            if k <= half_rows:
                return caluclate_kth_symbol(n - 1, k)
            else:
                return 1 - caluclate_kth_symbol(n - 1, k - half_rows)
        
        if n < 1:
            raise ValueError("n must be a positive integer.")
        if k < 1:
            raise ValueError("k must be a positive integer.") 
        return caluclate_kth_symbol(n, k)
```

## step2
### 読んだ
https://github.com/Yoshiki-Iwasa/Arai60/pull/39/files
https://github.com/hayashi-ay/leetcode/pull/46/files
https://github.com/olsen-blue/Arai60/pull/47
https://github.com/fuga-98/arai60/pull/46/files

### 感想
- ループでもかけるが、個人的には再帰の方が規則性に沿って扱う範囲を狭めていく部分が伝わりやすく綺麗だと思う。
- 2**(n-2)の部分を1<<(n-2)と書く人が多かった。シフト演算の方が早かった。
- ビットの反転も、1 - x ではなく、1^x 等でも表現できる。

> >>> def lshift_func(n):
> >>> ...     return 1 << n
> >>> def power_func(n):
> >>> ...     return 2 ** n

> >>> n = 100
> >>> print(f"lshift: {timeit.timeit(lambda: lshift_func(n), number=1000000):.6f}秒)
> >>> lshift: 0.149755秒
> >>> print(f"power:   {timeit.timeit(lambda: power_func(n), number=1000000):.6f}秒)
> >>> power:   0.324463秒


```py
class Solution:
    def kthGrammar(self, n: int, k: int) -> int:
        if n == 1:
            return 0
        half_nums = 1 << (n - 2)
        if k <= half_nums:
            return self.kthGrammar(n - 1, k)
        else:
            return 1 ^ self.kthGrammar(n - 1, k - half_nums)
```

## step3
2の冪乗はビット演算ではなく**で、反転は1^xの方がわかりやすいと思ったので、最終的に以下のようになった。

```py
class Solution:
    def kthGrammar(self, n: int, k: int) -> int:
        if n == 1:
            return 0
        half_nums = 2 ** (n - 2)
        if k <= half_nums:
            return self.kthGrammar(n - 1, k)
        else:
            return 1 ^ self.kthGrammar(n - 1, k - half_nums)
```
