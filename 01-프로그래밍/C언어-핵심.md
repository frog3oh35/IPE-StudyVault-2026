---
name: C언어-핵심
description: 정보처리기사 실기 C언어 코드 추적 핵심 — 포인터, 구조체, 재귀, 비트연산
source_pdf: 2026_정보처리기사_실기_기출문제집_핵심요약(20260223).pdf
part: 프로그래밍
keywords: [C언어, 포인터, 구조체, 재귀, 비트연산, 배열]
tags: [#programming, #c-lang, #priority-1, #concept]
---

# C언어 핵심

> [!important] 출제 빈도 ★★★★★
> 매 회차 2~3문제. 코드 실행 결과를 직접 추적하는 문제

---

## 포인터 (Pointer)

### 기본 개념

```c
int n = 5;
int *p = &n;   // p는 n의 주소를 저장

*p = 10;       // p가 가리키는 곳(n)의 값을 10으로 변경
printf("%d", n); // 10
```

### 포인터 연산

```c
int arr[] = {10, 20, 30};
int *p = arr;

printf("%d", *p);      // 10 (arr[0])
printf("%d", *(p+1));  // 20 (arr[1])
printf("%d", *(p+2));  // 30 (arr[2])
p++;
printf("%d", *p);      // 20 (이제 arr[1] 가리킴)
```

> [!warning] `*p++` vs `(*p)++`
> - `*p++` → p가 가리키는 값을 읽고, **포인터 p 자체**를 증가
> - `(*p)++` → **p가 가리키는 값**을 증가

### 이중 포인터

```c
int x = 5;
int *p = &x;
int **pp = &p;

printf("%d", **pp); // 5 (pp→p→x)
```

> [!example] 기출 (2025년 2회차 14번)
> ```c
> struct dat a[] = {{1,2},{3,4},{5,6}};
> struct dat *ptr = a;
> struct dat **pptr = &ptr;
> (*pptr)[1] = (*pptr)[2];  // a[1] = a[2]
> printf("%d %d", a[1].x, a[1].y); // 5 그리고 6
> ```

---

## 구조체 (struct)

```c
struct Node {
    int value;
    struct Node *next;  // 자기 참조 구조체 (링크드 리스트)
};

struct Node n1 = {10, NULL};
struct Node n2 = {20, NULL};
n1.next = &n2;

printf("%d", n1.next->value); // 20
```

### 멤버 접근 연산자

| 상황 | 연산자 | 예시 |
|------|--------|------|
| 일반 구조체 변수 | `.` | `node.value` |
| 포인터로 구조체 접근 | `->` | `ptr->value` |

> [!example] 기출 (2023년 3회차 5번)
> `d2->numPtr = &num;` — 포인터로 구조체 접근할 때 `->` 사용

### 함수 포인터 구조체

```c
struct fns {
    int* (*fn)(int*);  // 함수 포인터 멤버
};

int* dummy(int *d) { return d + 1; }

struct fns mine;
mine.fn = dummy;
int n[] = {16, 32};
printf("%x", *mine.fn(n)); // 32 = 0x20
```

> [!example] 기출 (2026년 1회차 12번)
> `*mine.fn(n)` → `dummy(n)` 실행 → `n+1`(n[1]의 주소) 반환 → `*`로 역참조 → `32` → 16진수 `0x20`

---

## 배열

### 2차원 배열과 포인터 배열

```c
int arr[3][3] = {1,2,3,4,5,6,7,8,9};
int *parr[2] = {arr[1], arr[2]};
// parr[0] → {4,5,6}
// parr[1] → {7,8,9}

printf("%d", parr[1][1]);     // 8  (arr[2][1])
printf("%d", *(parr[1]+2));   // 9  (arr[2][2])
printf("%d", **parr);         // 4  (arr[1][0])
// 결과: 8 + 9 + 4 = 21
```

> [!example] 기출 (2024년 2회차 13번) → 21

---

## 문자열 처리

```c
char* p = "KOREA";
printf("%s", p);      // KOREA
printf("%s", p+1);    // OREA  (포인터 이동)
printf("%c", *p);     // K
printf("%c", *(p+3)); // E
printf("%c", *p+4);   // O (K=75, 75+4=79=O)
```

> [!example] 기출 (2023년 3회차 10번)
> `*p+4` → K의 ASCII(75) + 4 = 79 = 'O' (포인터 이동이 아님!)

### 문자열 역방향 처리 (링크드 리스트 패턴)

```c
// 스택처럼 앞에 추가 → 역순 출력
struct node* func(char* s) {
    struct node *h = NULL, *n;
    while(*s) {
        n = malloc(sizeof(struct node));
        n->c = *s++;   // 현재 문자 저장, s를 다음으로
        n->p = h;      // 새 노드가 기존 헤드를 가리킴
        h = n;         // 헤드 갱신
    }
    return h;
}
// "BEST" → 출력: T,S,E,B 순 → "TSEB"
```

> [!example] 기출 (2025년 2회차 18번) → TSEB

---

## 재귀 함수

```c
int f(int n) {
    if(n <= 1) return 1;
    return n * f(n-1);  // 팩토리얼
}
printf("%d", f(7)); // 5040
```

---

## 비트 연산

| 연산자 | 의미 | 예시 |
|--------|------|------|
| `&` | AND | `5 & 3 = 1` (0101 & 0011 = 0001) |
| `\|` | OR | `5 \| 3 = 7` (0101 \| 0011 = 0111) |
| `^` | XOR | `5 ^ 3 = 6` (0101 ^ 0011 = 0110) |
| `~` | NOT (보수) | `~5 = -6` |
| `<<` | 왼쪽 시프트 | `2 << 2 = 8` (×4) |
| `>>` | 오른쪽 시프트 | `8 >> 2 = 2` (÷4) |

> [!example] 기출 (2024년 1회차 2번)
> `v3 = 29; v3 = v3 << 2;` → `29 * 4 = 116` → `v2+v3 = 35+116 = 151`

---

## static 변수

```c
int func() {
    static int x = 0;  // 함수 호출 종료 후에도 값 유지
    x += 2;
    return x;
}
// 호출 순서: 2 → 4 → 6 → 8
// 합계: 2+4+6+8 = 20
```

> [!example] 기출 (2024년 2회차 7번) → 20

---

## switch 문 fall-through

```c
switch(a) {
    case 1:
        b += 1;   // break 없으면 아래로 계속!
    case 11:
        b += 2;   // break 없으면 아래로 계속!
    default:
        b += 3;
    break;
}
// a=11이면: case 11 (b+=2), default (b+=3) 실행
```

> [!example] 기출 (2024년 3회차 18번)
> a=11, b=19 → case 11: b=21 → default: b=24 → a-b = 11-24 = **-13**

---

## 큐/스택 코드 추적

```c
// 큐 (환형)
void enq(Queue *q, int val) {
    q->a[q->rear] = val;
    q->rear = (q->rear + 1) % SIZE;  // 환형
}
int deq(Queue *q) {
    int val = q->a[q->front];
    q->front = (q->front + 1) % SIZE;
    return val;
}
```

> [!example] 기출 (2025년 2회차 12번)
> enq(1), enq(2), deq(), enq(3), deq()=2, deq()=3 → "2 그리고 3"

[[프로그래밍-연습문제]] | [[Java-핵심]] | [[Python-핵심]]
