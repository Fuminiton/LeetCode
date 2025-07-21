## 取り組み方
- step1: 5分以内に空で書いてAcceptedされるまで解く + テストケースと関連する知識を連想してみる
- step2: 他の方の記録を読んで連想すべき知識や実装を把握した上で、前提を置いた状態で最適な手法を選択し実装する
- step3: 10分以内に1回もエラーを出さずに3回連続で解く

## step1
開始地点に+1、終了地点に-1を加えると、0~制限時間最大までを1刻みで累積していけば、intervalの期間だけ+1された結果が得られるので、intervalsの全ての要素に対して適用する。

```py
class Solution:
    def minMeetingRooms(self, intervals: List[List[int]]) -> int:
        TIME_LIMITS = 1000000
        num_bookings = [0] * (TIME_LIMITS + 1)
        for start, end in intervals:
            num_bookings[start] += 1
            num_bookings[end] -= 1
        
        max_num_bookings = 0
        for i in range(TIME_LIMITS):
            num_bookings[i + 1] += num_bookings[i]
            max_num_bookings = max(max_num_bookings, num_bookings[i + 1])
        return max_num_bookings
```

## step2
### 読んだ
https://github.com/olsen-blue/Arai60/pull/57/files
https://github.com/hayashi-ay/leetcode/pull/62/files
https://github.com/Yoshiki-Iwasa/Arai60/pull/61/files

### 感想
- step1の方法は10^6より細かく時間を分けたい場合どうしようかと考えていた
- が、start or endのイベントタイプとその時刻をタプルとして管理すれば、時刻でソートした結果を見ていくだけで効率的に同時刻のミーティング数を数え上がられる
- step1ではケアできているが、同時刻に開始と終了がある場合の数え上げの順序は気をつけたい
- startとendの時刻をそれぞれ入れる配列を用意して、ポインターの進め方を工夫するやり方もよさそう
- 最小の値から時刻を見ていきたいので、ソートではなくヒープを使っている方もいたが、どちらでもよさそう

##### event_time, event_type のタプルで管理する
```py
class Solution:
    def minMeetingRooms(self, intervals: List[List[int]]) -> int:
        START = 1
        END = -1
        events = [] 
        for start_time, end_time in intervals:
            events.append((start_time, START))
            events.append((end_time, END))

        num_needed_rooms = 0
        num_ongoing = 0
        events.sort()
        for event_time, event_type in events:
            if event_type == END:
                num_ongoing -= 1
                continue
            num_ongoing += 1
            num_needed_rooms = max(num_needed_rooms, num_ongoing)

        return num_needed_rooms
```

##### タプルを使わず、startとend用に別々の配列を用意する
```py
class Solution:
    def minMeetingRooms(self, intervals: List[List[int]]) -> int:
        if not intervals:
            return 0

        start_times = []
        end_times = []
        for start_time, end_time in intervals:
            start_times.append(start_time)
            end_times.append(end_time)
        start_times.sort()
        end_times.sort()

        num_needed_rooms = 0
        num_ongoing = 0
        start_times_pointer = 0
        end_times_pointer = 0
        while start_times_pointer < len(start_times):
            if start_times[start_times_pointer] >= end_times[end_times_pointer]:
                num_ongoing -= 1
                end_times_pointer += 1
                continue
            num_ongoing += 1
            num_needed_rooms = max(num_needed_rooms, num_ongoing)
            start_times_pointer += 1

        return num_needed_rooms
```

## step3
num_occupiedにすれば、neededもoccupiedもroomの話をしているのかと分かるからよりよいと思った

```py
class Solution:
    def minMeetingRooms(self, intervals: List[List[int]]) -> int:
        MEETING_START = 1
        MEETING_END = -1

        events = []
        for start_time, end_time in intervals:
            events.append((start_time, MEETING_START))
            events.append((end_time, MEETING_END))
        
        num_occupied = 0
        num_needed = 0
        events.sort()
        for event_time, event_type in events:
            if event_type == MEETING_END:
                num_occupied -= 1
                continue
            num_occupied += 1
            num_needed = max(num_needed, num_occupied)
            
        return num_needed
```