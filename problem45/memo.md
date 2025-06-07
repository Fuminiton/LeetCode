## 取り組み方
- step1: 5分以内に空で書いてAcceptedされるまで解く + テストケースと関連する知識を連想してみる
- step2: 他の方の記録を読んで連想すべき知識や実装を把握した上で、前提を置いた状態で最適な手法を選択し実装する
- step3: 10分以内に1回もエラーを出さずに3回連続で解く
- step4: 浮動小数点記法まわりの知識を調べて整理する

## step1
- nが奇数の時、x * pow(x, n // 2) * pow(x, n // 2)
- nが偶数の時、pow(x, n // 2) * pow(x, n // 2)

とできるので、再帰で解く。計算量はO(log n)。
そのほか考えておきたいのは、

- x = 0 かつ n < 0 の時、分母が0になるのでエラーとしておきたい。
- x = 0 の時、早期リターン
- n < 0 の時、1.0 / pow(x, -n)
- n = 0 の時、1を返す

くらい。
xが小数の時に精度はどこまで求められて、どこまで精度の要求を満たせるのかは、step4でいろいろ調べる。

```py
class Solution:
    def myPow(self, x: float, n: int) -> float:
        def get_pow(x: float, n: int) -> float:
            if n == 0:
                return 1
            half_pow = get_pow(x, n // 2)
            if n % 2 == 1:
                return x * half_pow * half_pow
            else:
                return half_pow * half_pow

        if x == 0 and n < 0:
            raise ZeroDivisionError("When n < 0, x must be non-zero")
        if x == 0:
            if n == 0:
                return 1
            return 0

        if n < 0:
            return 1.0 / get_pow(x, -n)
        return get_pow(x, n)
```

## step2
### 読んだ
https://github.com/Mike0121/LeetCode/pull/17/files
https://github.com/TORUS0818/leetcode/pull/47/files
https://github.com/hroc135/leetcode/pull/43/files
https://github.com/SanakoMeine/leetcode/pull/9/files
https://github.com/fuga-98/arai60/pull/45/files

### 感想
- bit使った方法の理解が難しかった
  - n=5であれば、
    - 2進数で101
    - x^5 = {1 * x^(2^2)} * {0 * x^(2^1)} * {1 * x^(2^0)}
  - となることを利用して実装すればよい

- cpythonのfloat_powはcのpowを呼び出していた
  - cのdouble pow()も見たが、よくわからないので後で調べる

https://github.com/python/cpython/blob/main/Objects/floatobject.c#L810
https://git.musl-libc.org/cgit/musl/tree/src/math/pow.c#n255

- 冷静に考えると、x が float なのに、== を使っているのは微妙では？
  - 0.0 は Special Quantities として定義しているから問題なさそう

> The IEEE standard specifies the following special values (see TABLE D-2): ± 0, denormalized numbers, ± and NaNs (there is more than one NaN, as explained in the next section). These special values are all encoded with exponents of either emax + 1 or emin - 1 (it was already pointed out that 0 has an exponent of emin - 1).
https://docs.oracle.com/cd/E19957-01/806-3568/ncg_goldberg.html#875

  - 以下、実機確認。

> >>> x = float(0)
> >>> x == 0.0
> True
> >>> x == 0
> True
> >>> x == +0.0
> True
> >>> x == -0.0
> True

#### step1の洗練
ヘルパー関数をプライベートメソッドとして定義し直す。
half_powを偶奇で2回書くので、squaredを用意する。
0.0, 1.0のように明確にfloatであることがわかるようにする。
エッジケースの捌き方をいい感じに。

```py
class Solution:
    def _get_pow(self, x: float, n: int) -> float:
        if n == 0:
            return 1.0
        half_pow = self._get_pow(x, n // 2)
        half_pow_squared = half_pow * half_pow
        if n % 2 == 1:
            return x * half_pow_squared
        else:
            return half_pow_squared
    
    def myPow(self, x: float, n: int) -> float:
        if n < 0:
            if x == 0.0:
                raise ZeroDivisionError("When n < 0, x must be non-zero")
            return 1.0 / self._get_pow(x, -n)
        return self._get_pow(x, n)
```

#### ループ
```py
class Solution:
    def _get_pow(self, x: float, n: int) -> float:
        if n == 0:
            return 1.0
        power = 1.0
        base = x
        exponent = n
        while exponent > 0:
            if exponent % 2 == 1:
                power *= base
            base *= base
            exponent //= 2
        return power

    def myPow(self, x: float, n: int) -> float:
        if n < 0:
            if x == 0.0:
                raise ZeroDivisionError("When n < 0, x must be no-zero.")
            return 1.0 / self._get_pow(x, -n)
        return self._get_pow(x, n)
```

#### bitを使う方法
```py
class Solution:
    def _get_pow(self, x: float, n: int) -> float:
        if n == 0:
            return 1.0
        power = 1.0
        base = x
        n_mask = 1
        while n_mask <= n:
            if n_mask & n:
                power *= base
            base *= base
            n_mask <<= 1
        return power

    def myPow(self, x: float, n: int) -> float:
        if n < 0:
            if x == 0.0:
                raise ZeroDivisionError("When n < 0, x must be no-zero.")
            return 1.0 / self._get_pow(x, -n)
        return self._get_pow(x, n)
```

## step3
```py
class Solution:
    def _get_pow(self, base: float, exp: int) -> float:
        if exp == 0:
            return 1.0
        half_pow = self._get_pow(base, exp // 2)
        half_pow_squared = half_pow * half_pow
        if exp % 2 == 1:
            return base * half_pow_squared
        return half_pow_squared

    def myPow(self, x: float, n: int) -> float:
        if n < 0:
            if x == 0:
                raise ZeroDivisionError("When n < 0, x must be non-zero.")
            return 1.0 / self._get_pow(x, -n)
        return self._get_pow(x, n)
```

## step4
### まとめ
- IEEE 754-2008 の2進浮動小数点記法のサマリ
  - https://www.researchgate.net/figure/EEE-754-2008-standard-floating-point-number_fig9_326728372
    - 10進数 -> 2進数 -> 1.xxxx… * 2^yyyy… -> 符号ビット,yyyy…+offset,xx… ->符号,指数部,仮数部
- IEEE 754-2008 で定められた浮動小数点記法の基本方式
  - https://www.researchgate.net/figure/The-IEEE-754-2008-arithmetic-formats_tbl1_275406524
    - bin32なら符号1bit、指数部8bits、仮数部23bits。単精度とも言われるもの。
    - bin64なら符号1bit、指数部11bits、仮数部52bits。倍精度とも言われるもの。
- 小数を扱うときに気を付けておけるべきこと
  - https://docs.python.org/3/tutorial/floatingpoint.html

> >>> 0.1 + 0.1 + 0.1 == 0.3
> False
> >>> round(0.1, 1) + round(0.1, 1) + round(0.1, 1) == round(0.3, 1)
> False
> >>> math.isclose(0.1 + 0.1 + 0.1, 0.3)
> False

> For use cases which require exact decimal representation, try using the decimal module which implements decimal arithmetic suitable for accounting applications and high-precision applications.

> As that says near the end, “there are no easy answers.” Still, don’t be unduly wary of floating point! The errors in Python float operations are inherited from the floating-point hardware, and on most machines are on the order of no more than 1 part in 2**53 per operation. That’s more than adequate for most tasks, but you do need to keep in mind that it’s not decimal arithmetic and that every float operation can suffer a new rounding error.

### 感想
浮動小数点周りはSWEの入り口として知っておくべきことが多そう。
あと、一旦cを読めるように学習しないと、cpythonの読解の質が上がらなそう。