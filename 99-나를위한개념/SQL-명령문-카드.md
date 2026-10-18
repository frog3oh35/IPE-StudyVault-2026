---
name: SQL-명령문-카드
description: SQL 명령문(DDL · DML · DCL · TCL) A4 손필기용 요약 — 키워드 → 정답 표 + 명령어 카드(뼈대 → 예제 → 전/후 표 → 함정). ⚠️ = 틀렸던 것, 📌 = 10-18 오답
created: 2026-10-19
trigger: 10-18 SQL 전용 일일체크 명령문 0/4. A4에 옮겨 적으며 공부하는 스타일이라, 긴 참고 노트(SQL-명령문-개념정리)와 별도로 빠르게노트 + 카드 형식 요청
tags: [#나를위한개념, #빠르게노트, #database, #sql]
---

# SQL 명령문 카드 — DDL · DML · DCL

> [!note] 쓰는 법
> 표는 왼쪽(키워드)만 보고 오른쪽을 떠올린다. 🃏 카드는 **전/후 표를 손으로 직접 그려** 본다. ⚠️ = 틀렸던 것, 📌 = 10-18 오답.
> "왜 그런지" 막히면 → [[SQL-명령문-개념정리]]

## 0. 분류 — 무엇을 건드리나 ⚠️

| 키워드 | 정답 |
|---|---|
| **구조(그릇)**: 테이블·컬럼·뷰 만들기/바꾸기/없애기 | **DDL**: CREATE · ALTER · DROP · TRUNCATE |
| **데이터(행)**: 보기/넣기/고치기/지우기 | **DML**: SELECT · INSERT · UPDATE · DELETE |
| **권한**: 주기/뺏기 | **DCL**: GRANT · REVOKE |
| **작업 확정/취소** | **TCL**: COMMIT · ROLLBACK (보기에 TCL 없으면 DCL) |

> [!tip] 짝꿍으로 외우기
> 구조 ↔ 데이터: **CREATE ↔ INSERT**, **ALTER ↔ UPDATE**, **DROP ↔ DELETE**
> "열(컬럼)을 바꾸면 ALTER, 행의 값을 바꾸면 UPDATE"

---

## 1. DDL — 그릇

| 키워드 | 정답 |
|---|---|
| 테이블 만들기 | `CREATE TABLE 이름 (컬럼 타입 제약, ...)` |
| 유일 + NOT NULL | `PRIMARY KEY` |
| 중복 불가, NULL은 허용 | `UNIQUE` |
| 값 범위 제한 | `CHECK (조건)` |
| 다른 테이블 키 참조 | `FOREIGN KEY (컬럼) REFERENCES 테이블(컬럼)` |
| 📌 **컬럼 추가** | `ALTER TABLE t ADD 컬럼 타입` |
| 컬럼 타입 변경 | `ALTER TABLE t MODIFY 컬럼 타입` |
| 📌 **컬럼 삭제** | `ALTER TABLE t DROP COLUMN 컬럼` |
| 테이블 **통째로** 삭제 | `DROP TABLE t` |
| 구조는 두고 **행 전부** 삭제, 되돌리기 ❌ | `TRUNCATE TABLE t` |
| 📌 참조하는 게 있으면 **삭제 거부** | `RESTRICT` |
| 📌 참조하는 것까지 **같이** 삭제 | `CASCADE` |
| SELECT문에 이름 붙인 **가상 테이블** | `VIEW` (`CREATE VIEW v AS SELECT ...`) |

> [!warning] ALTER 안에 INSERT · DELETE는 못 들어간다 (📌 Q9)
> INSERT·DELETE는 행을 다루는 DML이다. ALTER 안에는 **ADD / MODIFY / DROP**만.

### 🃏 카드 1. ALTER TABLE

**뼈대**
```sql
ALTER TABLE 테이블 ADD 컬럼 타입;
ALTER TABLE 테이블 DROP COLUMN 컬럼;
```
**예제**
```sql
ALTER TABLE 학생 ADD 연락처 VARCHAR(20);
```
**전 → 후** (열이 늘어나고, 기존 행의 새 칸은 NULL)

| 전: 학번 | 이름 | → | 후: 학번 | 이름 | 연락처 |
|---|---|---|---|---|---|
| S1 | 김 | | S1 | 김 | NULL |
| S2 | 이 | | S2 | 이 | NULL |

**함정**: 행이 아니라 **열**이 늘어난다 → INSERT가 아니라 ADD.

### 🃏 카드 2. DROP ... RESTRICT / CASCADE 📌

**뼈대**
```sql
DROP TABLE 테이블 RESTRICT;   -- 또는 CASCADE
```
**예제**
```sql
CREATE VIEW v_영업 AS SELECT * FROM 사원 WHERE 부서 = '영업';
DROP TABLE 사원 ( ? );
```
**전 → 후**

| 옵션 | 사원 테이블 | v_영업 뷰 |
|---|---|---|
| (실행 전) | 있음 | 있음 (사원을 참조) |
| **RESTRICT** | 있음 (**거부됨**) | 있음 |
| **CASCADE** | 삭제 | **같이 삭제** |

**함정**: "테이블만 삭제, 뷰는 남음"은 **없다**. 뷰는 원본을 보는 창문이라 원본 없이 존재할 수 없다.

> [!tip] 삭제 3형제
> - **DROP** = 시트 삭제 (구조 ❌)
> - **TRUNCATE** = 시트 전체 비우기 (구조 ✅, ROLLBACK ❌)
> - **DELETE** = 행 골라 지우기 (구조 ✅, ROLLBACK ✅, 유일한 DML)

> [!tip] 뷰 특징 (기출 단골)
> 논리적 독립성 · 보안(필요한 열만 노출) · **ALTER 불가 → DROP 후 다시 CREATE** · 인덱스 불가 · 원본 삭제되면 사용 불가

근거: 기출 2023년 2회 `DROP VIEW 학생 ( )` → CASCADE

---

## 2. DML — 내용물

### 빈칸 단골 짝꿍 (기출 2024-2: VALUES · SELECT · FROM · SET)

| 명령 | 뼈대 |
|---|---|
| SELECT | `SELECT 컬럼` **FROM** `테이블 WHERE 조건` |
| INSERT | `INSERT` **INTO** `테이블 (컬럼들)` **VALUES** `(값들)` |
| INSERT + SELECT | `INSERT INTO 테이블 (컬럼들)` **SELECT** `... FROM ...` (VALUES 자리에 SELECT) |
| 📌 UPDATE | `UPDATE 테이블` **SET** `컬럼 = 새값 WHERE 조건` |
| DELETE | `DELETE` **FROM** `테이블 WHERE 조건` (DELETE 뒤 컬럼명 ❌) |

### 🃏 카드 3. INSERT

**예제**
```sql
INSERT INTO 학생 (학번, 이름) VALUES ('S3', '박');
```
**전 → 후** (행 하나 추가, 지정 안 한 컬럼은 NULL 또는 DEFAULT)

| 학번 | 이름 | 학년 |
|---|---|---|
| S1 | 김 | 2 |
| S2 | 이 | 3 |
| **S3** | **박** | **NULL** |

**함정**: 컬럼 목록을 생략하면 **모든 컬럼을 순서대로** 다 채워야 한다.

### 🃏 카드 4. UPDATE 📌 Q15

**예제** (두 문장 연속)
```sql
UPDATE 계좌 SET 잔액 = 잔액 + 500 WHERE 잔액 < 1000;
UPDATE 계좌 SET 잔액 = 잔액 * 2   WHERE 잔액 >= 1000;
```
**전 → 1번 후 → 2번 후**

| 번호 | 처음 | 1번 (< 1000이면 +500) | 2번 (≥ 1000이면 ×2) |
|---|---|---|---|
| 1 | 1000 | 1000 | **2000** |
| 2 | 500 | **1000** ✔ | **2000** ✔ |
| 3 | 2000 | 2000 | **4000** |

→ SUM = **8000**

**함정**
- 2번 문장은 **1번이 끝난 표** 기준 → 2번 계좌도 ×2
- WHERE를 빼면 **모든 행**이 바뀐다

### 🃏 카드 5. DELETE

**예제**
```sql
DELETE FROM 학생 WHERE 학년 = 3;
```
**전 → 후**

| 전: 학번 | 학년 | → | 후: 학번 | 학년 |
|---|---|---|---|---|
| S1 | 2 | | S1 | 2 |
| S2 | 3 | | ~~S2~~ | (삭제) |
| S3 | NULL | | S3 | NULL |

**함정**
- `학년 = 3`에서 NULL 행(S3)은 **안 지워진다** (NULL은 비교 불가)
- WHERE를 빼면 전부 지워지지만 구조는 남는다

---

## 3. DCL — 권한 📌

| 키워드 | 정답 |
|---|---|
| 권한 **주기** | `GRANT 권한 ON 테이블` **TO** `사용자` |
| 권한 **뺏기** | `REVOKE 권한 ON 테이블` **FROM** `사용자` |
| 받은 권한을 **남에게 다시 줄 수 있게** | `WITH GRANT OPTION` |
| 나눠 준 권한까지 **연쇄 회수** | `REVOKE ... CASCADE` |
| 모든 사용자에게 | `TO PUBLIC` |
| 권한을 묶어서 관리 | `ROLE` (= 보안의 RBAC) |

> [!tip] TO / FROM
> 우리말 그대로. kim**에게** 준다 = TO, kim**으로부터** 뺏는다 = FROM

### 🃏 카드 6. GRANT → REVOKE CASCADE 📌 Q6

**예제**
```sql
① GRANT UPDATE ON 상품 TO kim WITH GRANT OPTION;   -- DBA
② GRANT UPDATE ON 상품 TO lee;                     -- kim
③ REVOKE UPDATE ON 상품 FROM kim CASCADE;          -- DBA
```
**권한 상태 변화**

| 시점 | kim | lee |
|---|---|---|
| ① 후 | ✅ UPDATE + 줄 자격 | ❌ |
| ② 후 | ✅ | ✅ (kim에게서 받음) |
| ③ 후 | ❌ | ❌ **연쇄 회수** |

**함정**
- WITH GRANT OPTION이 없었다면 ②는 **실행 자체가 안 된다**
- CASCADE는 DROP에서와 같은 뜻: "딸린 것까지 줄줄이"

---

## 4. TCL — 짧게

| 키워드 | 정답 |
|---|---|
| 작업 확정 (→ 지속성) | `COMMIT` |
| 마지막 COMMIT 이후 전부 취소 (→ 원자성) | `ROLLBACK` |
| 중간 저장 지점까지만 취소 | `SAVEPOINT s1` → `ROLLBACK TO s1` |
| ROLLBACK이 **안 되는** 것 | DDL (자동 COMMIT) → TRUNCATE·DROP은 되돌릴 수 없음 |

---

## 📌 10-18 오답 모음

| Q | 몰랐던 것 | 한 줄 정답 |
|---|---|---|
| 1 | AND / OR 우선순위 | **NOT > AND > OR**. AND 양쪽부터 괄호로 묶기 |
| 2 | `LIKE '%S_'` | `%`는 **0글자도** 됨 → 'S2'도 포함 |
| 5 | `= NULL` vs `IS NULL` | `= NULL`은 **항상 0행**. NULL 찾기는 `IS NULL` |
| 6 | REVOKE CASCADE | kim이 나눠 준 lee 권한까지 **연쇄 회수** |
| 8 | INNER / CROSS JOIN | INNER: NULL·짝 없는 값 탈락 / CROSS: 모든 조합 = **행 수 곱** |
| 9 | ALTER | 컬럼 추가 **ADD**, 삭제 **DROP COLUMN** (INSERT·DELETE ❌) |
| 10 | 뷰 + RESTRICT | 참조 중이면 **삭제 거부** (CASCADE면 같이 삭제) |
| 11 | `> ALL` | **`> MAX`** (`> ANY` = `> MIN`, `= ANY` = IN) |
| 12 | ORDER BY DESC | **DESC = 큰 것부터**, 키워드는 바로 앞 컬럼에만 |
| 15 | UPDATE 연속 | 다음 문장은 **바뀐 표** 기준. 문장마다 표 새로 적기 |

관련: [[SQL-명령문-개념정리]] (막힐 때 "왜?" 찾기) · [[../02-SQL-DB/SQL-핵심]] · [[DB-기본개념]] · [[../07-일일체크/2026-10-18]] · [[../concepts/02-SQL-DB]]
