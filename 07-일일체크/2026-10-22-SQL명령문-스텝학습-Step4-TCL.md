# SQL 명령문 스텝 학습 ④ Step 4 (TCL) — 2026-10-22

**방식**: [[../99-나를위한개념/SQL-명령문-개념정리]]를 순서대로 같이 읽고, 단계마다 설명 → 문제(정처기 실기 스타일). 기준은 [[2026-10-18]] 오답.
**이전 노트**: Step 3 (DCL) → [[2026-10-22-SQL명령문-스텝학습-Step3-DCL]]

## 진행 현황

| Step | 범위 | 문제 | 정답 |
|---|---|---|---|
| 4 | TCL (COMMIT · ROLLBACK · SAVEPOINT · DDL 자동 COMMIT) | | |

## 🟨 이전 노트에서 넘어온 재확인 대상

- **S1-9** TRUNCATE 후 ROLLBACK — DDL은 자동 COMMIT → 이 노트 ③에서 재확인

## 🆕 새로 발견한 모르는 것

> 10-18 오답 목록에 **없던** 구멍만 여기에 모은다.

(진행하면서 추가)

## ✅ 10-18 오답 복구 확인

(진행하면서 추가)

---

## 📖 스텝별 설명

### Step 4. TCL — 작업 묶음 확정·취소 (COMMIT / ROLLBACK / SAVEPOINT)

기출: 시나공 158 (필기 24.7, 23.5, 20.8, 20.6) · S1-9 (TRUNCATE 후 ROLLBACK)

#### ① 트랜잭션 = 한 덩어리로 처리할 작업 묶음

- 계좌이체: "A에서 1만원 빼기" + "B에 1만원 넣기"는 **둘 다 되거나 둘 다 안 돼야** 한다
- 하나만 반영되면 **비일관성(Inconsistency)** → 그래서 일부만 끝난 트랜잭션은 ROLLBACK

#### ② 세 명령

- `COMMIT` = **Ctrl+S**. 지금까지 바꾼 것을 디스크에 확정. 이후 ROLLBACK으로 못 되돌림
- `ROLLBACK` = **Ctrl+Z (마지막 저장 지점까지)**. COMMIT 안 된 변경을 전부 취소
- `SAVEPOINT 이름` = 중간 저장점. `ROLLBACK TO 이름` → 그 지점까지만 되돌림

```sql
DELETE FROM 사원 WHERE 이름 = '임꺽정';    -- 8 → 7행
SAVEPOINT s1;
DELETE FROM 사원 WHERE 부서 = '기획';      -- 7 → 4행
ROLLBACK TO s1;                            -- 기획 3명 복구 → 7행
ROLLBACK;                                  -- 임꺽정도 복구 → 8행
```

```sql
DELETE FROM 사원 WHERE 이름 = '임꺽정';
COMMIT;                                    -- 확정
ROLLBACK;                                  -- 되돌릴 게 없음 → 7행 그대로
```

#### ③ DDL은 자동 COMMIT (S1-9)

- CREATE · ALTER · DROP · **TRUNCATE** 는 실행되는 순간 확정 → ROLLBACK ❌
- 그래서 `TRUNCATE` 후 `ROLLBACK` → 행이 돌아오지 않는다 / `DELETE` 후 `ROLLBACK` → 돌아온다
- 주의: DDL이 실행되면 **그 앞에 COMMIT 안 한 DML까지** 같이 확정되는 DB가 많다 (Oracle)

#### ④ 함정 정리

- COMMIT 뒤의 ROLLBACK은 COMMIT 이전으로 못 감
- `ROLLBACK TO s1` 은 s1 **이후**만 취소, s1 이전 변경은 남아 있음
- 실행 결과(행 수) 문제 → COMMIT / SAVEPOINT 위치에 선을 그어 놓고 따라가기

---


## 문제 기록
