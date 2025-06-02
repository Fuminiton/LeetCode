## 取り組み方
- step1: 5分以内に空で書いてAcceptedされるまで解く + テストケースと関連する知識を連想してみる
- step2: 他の方の記録を読んで連想すべき知識や実装を把握した上で、前提を置いた状態で最適な手法を選択し実装する
- step3: 10分以内に1回もエラーを出さずに3回連続で解く

## step1
1日に取り込める最小のキャパを求める。
キャパとして考えられるmax(weights)～sum(weights)の中で最初に満たす値が最小のキャパ。
これを二分探索で求める。

キャパとして可能かどうかの判定は、以下。

1. ベルトコンベアの先から貸物の重さを足していく
2. 重さの合計がキャパを超えるかみる
3. 超えたら、一つ前までの貨物で重さをリセットし、日を進める
4. 1~3.をループしてすべての貨物を見終わったときに、所要日数がdays以内ならオッケー

時間計算量は、O(weights.length * log(max(weights) * weights.length)) なので数秒もかからない見込み。

bisect_leftを使うか迷ったが、max(weights)～sum(weights)の配列を作る方法しか思いつかなかったので、
余計に大きな配列を作らなくて良いという観点で自前の二分探索で書く方が良いかと思った。

### bisect_leftを使わない

```py
class Solution:
    def shipWithinDays(self, weights: List[int], days: int) -> int:
        def can_ship(capacity: int) -> bool:
            processing_days = 1
            loaded = 0
            for weight in weights:
                if weight + loaded > capacity:
                    processing_days += 1
                    loaded = 0
                loaded += weight
            
            return processing_days <= days

        maybe_first_can_ship_capacity = max(weights)
        can_ship_capacity = sum(weights)
        while maybe_first_can_ship_capacity < can_ship_capacity:
            mid = (maybe_first_can_ship_capacity + can_ship_capacity) // 2
            if can_ship(mid):
                can_ship_capacity = mid
            else:
                maybe_first_can_ship_capacity = mid + 1

        return maybe_first_can_ship_capacity
```

### bisect_leftを使う

```py
class Solution:
    def shipWithinDays(self, weights: List[int], days: int) -> int:
        def can_ship(capacity: int) -> bool:
            processing_days = 1
            loaded = 0
            for weight in weights:
                if weight + loaded > capacity:
                    processing_days += 1
                    loaded = 0
                loaded += weight
            
            return processing_days <= days

        maybe_min_capacity = max(weights)
        maybe_max_capacity = sum(weights)
        candidates= range(maybe_min_capacity, maybe_max_capacity + 1)
        min_capacity_index = bisect_left(candidates, True, key=can_ship)
        return candidates[min_capacity_index]
```

## step2
### 読んだ
- https://github.com/fuga-98/arai60/pull/44/files
- https://github.com/fhiyo/leetcode/pull/45/files
- https://github.com/sakupan102/arai60-practice/pull/45/files
- https://github.com/nittoco/leetcode/pull/47/files
- https://github.com/olsen-blue/Arai60/pull/44/files
- https://github.com/hroc135/leetcode/pull/42/files

### 感想
- daysが0以下は早期リターンすべきだった
- 変数名が冗長すぎるかもしれない
- rangeでリストが作られると思っていたが、実際に作られるのはstartとstop地点、ステップと長さを持ったrange objectらしい
  - ので、自分がstep1時に考えていた「bisect_leftを使うか迷ったが、max(weights)～sum(weights)の配列を作る方法しか思いつかなかった」は杞憂だった
  - 必要になった時点で初めて計算するという遅延評価というものらしい
  - generatorとかiteratorも同じか
- `range(sum(weights) + 1)`を与えて、`lo=max(weights)`とすればindexではなくて、min_capacityそのものを探せて綺麗な実装だと思った

https://github.com/python/cpython/blob/9ad0c7b0f14c5fcda6bfae6692c88abb95502d38/Objects/rangeobject.c#L68
>   obj->start = start;
>   obj->stop = stop;
>   obj->step = step;
>   obj->length = length;

実際に中身を見てみた

```py
>>> start = 0
>>> end = 50000000 + 1
>>> range_object = range(start, end + 1)
>>> list_object = list(range(start, end + 1))
>>> import sys
>>> print(f"range object: {sys.getsizeof(range_object)} bytes")
range object: 48 bytes
>>> print(f"list object: {sys.getsizeof(list_object)} bytes")
list object: 400000072 bytes
```

```py
class Solution:
    def shipWithinDays(self, weights: List[int], days: int) -> int:
        def can_ship(capacity: int) -> bool:
            processing_days = 1
            loaded = 0
            for w in weights:
                if w + loaded > capacity:
                    processing_days += 1
                    loaded = 0  # reset
                loaded += w
            return processing_days <= days

        if days < 1:
            raise ValueError("days must be greater than 1.")

        min_capacity = bisect_left(
            range(sum(weights) + 1), 
            True,
            lo=max(weights),
            key=can_ship)
        return min_capacity
```

### step3
```py
class Solution:
    def shipWithinDays(self, weights: List[int], days: int) -> int:
        def can_ship_within_days(capacity: int) -> bool:
            task_duration_days = 1
            loaded = 0
    
            for w in weights:
                if w + loaded > capacity:
                    task_duration_days += 1
                    loaded = 0  # reset
                loaded += w
    
            return task_duration_days <= days
        
        min_capacity_candidates = range(sum(weights))
        return bisect_left(
            min_capacity_candidates,
            True,
            lo=max(weights),
            key=can_ship_within_days
        )
```