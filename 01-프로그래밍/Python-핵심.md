---
name: Python-핵심
description: 정보처리기사 실기 Python 코드 추적 핵심 — 슬라이싱, 컬렉션, 클래스
source_pdf: 2026_정보처리기사_실기_기출문제집_핵심요약(20260223).pdf
part: 프로그래밍
keywords: [Python, 슬라이싱, 딕셔너리, 세트, 리스트, 클래스, 람다]
tags: [#programming, #python, #priority-1, #concept]
---

# Python 핵심

> [!important] 출제 빈도 ★★★★
> 매 회차 1~2문제. 코드 추적. 슬라이싱과 컬렉션 조작이 자주 출제

---

## 슬라이싱 (Slicing)

### 기본 문법: `리스트[start:stop:step]`

```python
lst = [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]

lst[::2]   # [0,2,4,6,8]   — 처음부터 2칸씩
lst[1::2]  # [1,3,5,7,9]   — 인덱스1부터 2칸씩
lst[::-1]  # [9,8,7,6,5,4,3,2,1,0]  — 역순
lst[::-2]  # [9,7,5,3,1]   — 역순으로 2칸씩
```

> [!example] 기출 (2026년 1회차 13번)
> ```python
> lst = list(range(10))       # [0~9]
> for c in lst[::-2]:         # [9,7,5,3,1]
>     print(c, end='A')
> print()
> # 출력: 9A7A5A3A1A
> ```

### 문자열 슬라이싱

```python
a = "engineer information processing"
b = a[:3]   # "eng"
c = a[4:6]  # "ne"  (인덱스4='n', 5='e')
d = a[28:]  # "ing"
e = b+c+d   # "engneing"
```

> [!example] 기출 (2023년 2회차 19번) → **engneing**

---

## 리스트 (List)

```python
lst = [1, 2, 3]

# 역순 함수
def func(lst):
    for i in range(len(lst) // 2):
        lst[i], lst[-i-1] = lst[-i-1], lst[i]

func([1,2,3,4,5,6])  # [6,5,4,3,2,1]
# sum([6,4,2]) - sum([5,3,1]) = 12-9 = 3
```

> [!example] 기출 (2024년 3회차 2번) → **3**

### 얕은 복사 (Shallow Copy)

```python
m = [[x] for x in a]   # 새 리스트지만 요소는 공유
b = m[:]                # 얕은 복사
b[i+1] += b[i]         # m과 b의 같은 요소를 공유!
```

> [!example] 기출 (2026년 1회차 14번) → **10**
> `m[:]`은 얕은 복사 — `b[i]`가 `m[i]`와 같은 객체. `b[i+1] += b[i]`가 `m[i+1]`도 수정

---

## 딕셔너리 (Dictionary)

```python
data = [[3,5,2,4,1], [4,5,1], [4,4,1,5,4], [4,5]]
result = {}
for index, lis in enumerate(data):
    list_sum = sum(lis)
    list_len = len(lis)
    result[index] = (list_sum, list_len)
print(result)
# {0:(15,5), 1:(10,3), 2:(18,5), 3:(9,2)}
```

> [!example] 기출 (2025년 3회차 9번)

### 딕셔너리 컴프리헨션

```python
dst = {i: i*2 for i in [1,2,3]}
# {1:2, 2:4, 3:6}
```

---

## 세트 (Set)

```python
a = {'한국', '중국', '일본'}
a.add('베트남')
a.add('중국')     # 중복 → 무시
a.remove('일본')
a.update({'홍콩', '한국', '태국'})  # 여러 요소 추가
# {'한국', '중국', '베트남', '홍콩', '태국'}
```

> [!example] 기출 (2023년 1회차 15번)

### 세트 교집합

```python
s & set(dst.values())  # & = 교집합
```

> [!example] 기출 (2025년 2회차 17번)
> ```python
> lst = [1,2,3]
> dst = {i: i*2 for i in lst}  # {1:2, 2:4, 3:6}
> s = set(dst.values())         # {2,4,6}
> lst[0] = 99
> dst[2] = 7
> s.add(99)
> # s = {2,4,6,99}, set(dst.values()) = {2,7,6}
> # s & set(dst.values()) = {2,6}
> print(len(s & set(dst.values())))  # 2
> ```

---

## 입력 처리

```python
num1, num2 = input().split()  # split()으로 공백 기준 분리
num1 = int(num1)
```

> [!example] 기출 (2023년 3회차 14번) → `split`

---

## 문자열 메서드

```python
a = "abdcabcabca"
p1 = "ab"
# f-string
out = f"ab{fnCalculation(a,p1)}ca{fnCalculation(a,p2)}"
```

### 자주 쓰는 메서드

| 메서드 | 설명 |
|--------|------|
| `split(sep)` | 구분자로 분리 |
| `join(iterable)` | 요소를 연결 |
| `[::-1]` | 문자열 역순 |
| `c not in 'ong'` | 특정 문자 제외 필터 |

> [!example] 기출 (2026년 1회차 8번)
> ```python
> i = "HumanDev"
> y = ''.join(i.split())   # "HumanDev" (공백 없으므로 동일)
> z = ''.join(c for c in y[::-1] if c not in 'ong')
> # y[::-1] = "veDnamuH"
> # 'o','n','g' 제외 → "veDamuH"
> print(z)  # veDamuH
> ```

---

## 클래스 (Class)

```python
class Node:
    def __init__(self, value):
        self.value = value
        self.children = []

def calc(node, level=0):
    if node is None:
        return 0
    return (node.value if level % 2 == 1 else 0) + sum(calc(n, level+1) for n in node.children)
```

> [!example] 기출 (2025년 1회차 17번)
> 트리에서 홀수 레벨(1, 3...)의 노드 값만 합산
> 리스트 `[3,5,8,12,15,18,21]` → 레벨1: 5+12+15+18+21 → 합: 5+8=13 → **13**

---

## type() 비교

```python
def func(value):
    if type(value) == type(100):    # int
        return 100
    elif type(value) == type(""):   # str
        return len(value)
    else:
        return 20

a = '100.0'  # str → len('100.0')=5
b = 100.0    # float → 20
c = (100,200)# tuple → 20
print(func(a) + func(b) + func(c))  # 5+20+20=45
```

> [!example] 기출 (2024년 3회차 10번) → **45**

---

## range() & 반복문 패턴

```python
for i in range(1, len(lst)):         # 1부터 시작
for i in range(len(lst) // 2):      # 절반만
for i, val in enumerate(data):      # 인덱스+값 동시
```

[[프로그래밍-연습문제]] | [[C언어-핵심]] | [[Java-핵심]]
