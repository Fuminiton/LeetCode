# はじめに
## 取り組み方
- step1: 5分以内に空で書いてAcceptedされるまで解く
- step2: discordとwebで検索した結果を踏まえ、コードの洗練を洗練させる + 何を悪いと思ったか、何を避けたいと思ったか等感じたことを記載する
- step3: 10分以内に1回もエラーを出さずに3回連続で解く

## step1
```python
class Solution:
    def isValid(self, s: str) -> bool:
        brackets_mapping = {
            "(": ")",
            "[": "]",
            "{": "}"
        }
        opening_stack = []
        for bracket in s:
            if bracket in "([{":
                opening_stack.append(bracket)
            else:
                if len(opening_stack) == 0:
                    return False
                opening = opening_stack.pop()
                if brackets_mapping[opening] != bracket:
                    return False
        if len(opening_stack) == 0:
            return True
        else:
            return False
```

### 思考過程
- "(", "[", "{" ならスタックに積んで行って、そうでないならスタックポインタの位置のデータpopし、mappingした値を使っていい感じに比較する
- 一方、スタックからpopできないケースやpopしきれないケースがあることに注意したい："(((((("や"}}}}}"

### 感想
- 与えられる引数が「s」という命名で微妙だと感じた

## step2
```python
class Solution:
    def isValid(self, brackets: str) -> bool:
        brackets_mapping = {
            "(": ")",
            "[": "]",
            "{": "}"
        }
        opening_stack = []
        for bracket in brackets:
            if bracket in "([{":
                opening_stack.append(bracket)
            else:
                if len(opening_stack) == 0:
                    return False
                opening = opening_stack.pop()
                closing = brackets_mapping[opening]
                if closing != bracket:
                    return False
        if len(opening_stack) == 0:
            return True
        else:
            return False
```
### 感想
- 引数の命名を変えられるそうなので、変えた

## step3
```python
class Solution:
    def isValid(self, brackets: str) -> bool:
        mapping = {
            "(": ")",
            "[": "]",
            "{": "}"
        }
        brackets_stack = []
        for bracket in brackets:
            if bracket in mapping:
                brackets_stack.append(bracket)
            else:
                if len(brackets_stack) == 0:
                    return False
                if mapping[brackets_stack[-1]] != bracket:
                    return False
                brackets_stack.pop()
        if len(brackets_stack) != 0:
            return False
        return True
```

### 感想

