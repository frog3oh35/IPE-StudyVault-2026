# SQL 명령문 스텝 학습 ③ Step 3 (DCL) — 2026-10-22

**방식**: [[../99-나를위한개념/SQL-명령문-개념정리]]를 순서대로 같이 읽고, 단계마다 설명 → 문제(정처기 실기 스타일). 기준은 [[2026-10-18]] 오답.
**이전 노트**: Step 2 (DML) → [[2026-10-21-SQL명령문-스텝학습-Step2-DML]]
**다음 노트**: Step 4 (TCL) → [[2026-10-22-SQL명령문-스텝학습-Step4-TCL]]

## 진행 현황

| Step | 범위 | 문제 | 정답 |
|---|---|---|---|
| 3 | DCL (GRANT · REVOKE · WITH GRANT OPTION · CASCADE · ROLE) | | |

## 🟨 이전 노트에서 넘어온 재확인 대상

- **RESTRICT/CASCADE** (S1-8 · S1-11) — RESTRICT는 딸린 게 있는 **그 행만** 거부 / CASCADE는 **나를 보는 쪽**으로만 퍼짐 → 이 노트 ④ `REVOKE ... CASCADE`에서 재확인
- 10-18 Q6 (REVOKE ... CASCADE) 오답

## 🆕 새로 발견한 모르는 것

> 10-18 오답 목록에 **없던** 구멍만 여기에 모은다.

(진행하면서 추가)

## ✅ 10-18 오답 복구 확인

(진행하면서 추가)

---

## 📖 스텝별 설명

### Step 3. DCL — 권한 주고 뺏기 (GRANT / REVOKE)

기출: 시나공 156·157 (필기 위주, 실기 빈칸 2023-2 **CASCADE**) · 10-18 Q6

#### ① 뼈대

```sql
GRANT  권한들 ON 개체 TO   사용자 [WITH GRANT OPTION];
REVOKE [GRANT OPTION FOR] 권한들 ON 개체 FROM 사용자 [CASCADE];
```

- **GRANT ... TO** (~에게 준다) / **REVOKE ... FROM** (~로부터 뺏는다) — 전치사 짝이 빈칸 단골
- 권한 종류: `ALL` · `SELECT` · `INSERT` · `DELETE` · `UPDATE`
- 구글 시트의 **공유 설정**: "NABI에게 편집 권한 주기"

#### ② WITH GRANT OPTION — 받은 권한을 남에게 다시 줄 수 있는 권한

```sql
GRANT ALL ON 고객 TO NABI WITH GRANT OPTION;   -- 시나공 예제 1
```

- NABI는 <고객>을 마음대로 쓸 수 있고, **다른 사람에게도** 권한을 나눠 줄 수 있다

#### ③ GRANT OPTION FOR — "나눠 주는 권한"만 뺏기

```sql
REVOKE GRANT OPTION FOR UPDATE ON 고객 FROM STAR;   -- 시나공 예제 2
```

- STAR는 UPDATE는 **계속 할 수 있지만**, 남에게 UPDATE 권한을 주는 것만 못 하게 됨

#### ④ CASCADE — 내가 준 권한을 타고 연쇄로 회수 (Q6)

```
DBA → NABI (WITH GRANT OPTION) → KIM
REVOKE SELECT ON 고객 FROM NABI CASCADE;
→ NABI 회수 + NABI가 KIM에게 준 것도 회수
```

- **CASCADE = 나에게서 뻗어 나간 쪽으로만** 퍼진다 (🟨 재확인: DROP의 CASCADE와 같은 뜻 "연쇄로 같이")
- CASCADE 없이(RESTRICT 성격) 회수하려는데 NABI가 이미 남에게 줬다면 → DB에 따라 **거부**

#### ⑤ ROLE — 권한 묶음

```sql
CREATE ROLE 사원역할;
GRANT SELECT, INSERT ON 고객 TO 사원역할;
GRANT 사원역할 TO KIM, LEE;
```

- 사람마다 하나씩 주지 않고 **역할**에 묶어서 한 번에 부여 (회사의 "직급별 권한"과 같음)

#### ⑥ 함정 정리

- GRANT는 **TO**, REVOKE는 **FROM**
- WITH GRANT OPTION(줄 때) ↔ GRANT OPTION FOR(뺏을 때) 위치 · 단어 순서 주의
- 시나공은 **COMMIT · ROLLBACK도 DCL**에 넣는다 → 보기에 TCL이 없으면 DCL

---

---


## 문제 기록
