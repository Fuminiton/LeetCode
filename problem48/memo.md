## 取り組み方
- step1: 5分以内に空で書いてAcceptedされるまで解く + テストケースと関連する知識を連想してみる
- step2: 他の方の記録を読んで連想すべき知識や実装を把握した上で、前提を置いた状態で最適な手法を選択し実装する
- step3: 10分以内に1回もエラーを出さずに3回連続で解く

## step1
現在の会議の終了時刻よりも先に、次の会議が始まってしまったらアウト。
したがって、startを起点にintervalsをソートした上で、前の会議が割り込んでこないかをチェックしていけばよい。

社員10^5人くらいの規模で1人が1日5会議くらいあると考えても、1秒以内にはチェックが終わってほしい。
時間計算量はintervalsをソートするのに最も時間がかかるが、n = 5 * 10^5 の nlogn で  3*10^6 くらいなので、ぎりぎり1秒くらいで返せそうか。

```py
class Solution:
    def canAttendMeetings(self, intervals: List[List[int]]) -> bool:
        sorted_intervals = sorted(intervals, key=lambda x: x[0])
        for i in range(len(sorted_intervals) - 1):
            end_time = sorted_intervals[i][1]
            next_start_time = sorted_intervals[i + 1][0]
            if not (end_time <= next_start_time):
                return False
        return True
```

## step2
### 読んだ
https://github.com/shining-ai/leetcode/pull/55/files
https://github.com/goto-untrapped/Arai60/pull/60/files
https://github.com/hayashi-ay/leetcode/pull/59/files

### 感想
- ソートは別にヒープでもよい
- 元のリストを直接ソートしてもよいなら.sort() メソッドを使うのも良さそう
- for i ではなくて、start, endを取り出す方が理解しやすい
- 先にミーティングを入れた方を優先させるとなった場合に、ソート前の情報は持っておきたいと思うので、コピーを作ってソートする

```py
class Solution:
    def canAttendMeetings(self, intervals: List[List[int]]) -> bool:
        if len(intervals) == 0:
            return True

        sorted_intervals = sorted(intervals)
        previous_end_time = sorted_intervals[0][1]
        for start_time, end_time in sorted_intervals[1:]:
            if start_time < previous_end_time:
                return False
            previous_end_time = end_time
        return True
```

## step3
```py
class Solution:
    def canAttendMeetings(self, intervals: List[List[int]]) -> bool:
        sorted_intervals = sorted(intervals)
        previous_end = 0
        for start, end in sorted_intervals:
            if not previous_end <= start:
                return False
            previous_end = end
        return True
```