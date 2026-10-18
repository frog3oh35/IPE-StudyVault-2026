---
name: SQL-명령문-개념정리
description: SQL 명령문(DDL · DML · DCL · TCL) 개념을 "왜?"까지 풀어 쓴 참고 노트 — 구조(그릇) vs 데이터(내용물) vs 권한. ⚠️ = 틀렸거나 아직 확인 안 한 것
created: 2026-10-18
trigger: 10-18 SQL 전용 일일체크에서 명령문(ALTER · DROP RESTRICT · 뷰 · UPDATE · GRANT/REVOKE) 0/4 전부 모름. 본인: "DCL DDL (하나 더) 어느 쪽을 아예 모르는지 느낌이 오지"
tags: [#나를위한개념, #database, #sql, #critical]
---

# SQL 명령문 — DDL · DML · DCL 개념 정리

> [!note] 이 노트를 만든 이유
> 2026-10-18 SQL 전용 체크에서 SELECT를 **읽는** 문제는 5/8로 버텼는데, 무언가를 **만들고 · 바꾸고 · 지우고 · 권한을 주는** 명령문은 4문제 전부 몰랐다. DDL · DML · DCL 중 어느 쪽을 아예 모르는지 본인도 느꼈다.
> SELECT를 **읽는** 법(JOIN · GROUP BY · 집계함수)은 [[../02-SQL-DB/SQL-핵심]]과 [[DB-기본개념]]에 있다. 이 노트는 **명령문**만 다루고, 막힐 때 "왜?"를 찾아보는 **참고용**이다. A4에 옮겨 적는 요약본은 [[SQL-명령문-카드]]에 있다.

---

## 0. 큰 그림 — "무엇을 건드리나"로 나눈다 ⚠️

테이블을 **엑셀 시트**라고 생각하면 된다.

| 분류 | 풀네임 | 건드리는 것 | 엑셀로 치면 | 명령어 |
|---|---|---|---|---|
| **DDL** | Data **Definition** Language | **그릇** (테이블 · 컬럼 · 뷰 · 인덱스) | 시트 만들기, 열 추가, 시트 삭제 | CREATE · ALTER · DROP · TRUNCATE |
| **DML** | Data **Manipulation** Language | **내용물** (행 데이터) | 칸에 값 넣기 · 고치기 · 지우기 · 보기 | SELECT · INSERT · UPDATE · DELETE |
| **DCL** | Data **Control** Language | **사람** (권한) | 공유 설정 (편집 권한 주기) | GRANT · REVOKE |
| TCL | Transaction Control | **작업 묶음** | Ctrl+S / Ctrl+Z | COMMIT · ROLLBACK · SAVEPOINT |

> [!tip] 왜 굳이 나누나?
> 하는 일의 **무게**가 달라서다.
> - DDL은 구조를 바꾸므로 실행 즉시 확정(자동 COMMIT)되고 되돌리기 어렵다.
> - DML은 트랜잭션 안에서 실행돼서 ROLLBACK으로 되돌릴 수 있다.
> - DCL은 보안 담당자(DBA)의 일이라 따로 묶는다.

> [!warning] TCL은 교재마다 위치가 다르다
> 정처기 교재·기출은 **"DCL = GRANT, REVOKE, COMMIT, ROLLBACK"**처럼 TCL을 DCL에 넣는 경우가 많다. 분류 문제에서 TCL 보기가 따로 없으면 COMMIT·ROLLBACK은 **DCL**로 답한다.

### 그릇 ↔ 내용물 짝꿍표 (이것만은 꼭)

| 하고 싶은 일 | 그릇 (DDL) | 내용물 (DML) |
|---|---|---|
| 만들기 / 넣기 | `CREATE TABLE`, `ALTER ... ADD` | `INSERT INTO` |
| 고치기 | `ALTER ... MODIFY` | `UPDATE ... SET` |
| 지우기 | `DROP`, `ALTER ... DROP` | `DELETE FROM` |
| 보기 | — | `SELECT` |

> [!tip] 한 줄 요약
> **구조를 바꾸면 ALTER, 값을 바꾸면 UPDATE.** "열(컬럼)"은 구조이고 "행"은 내용물이다.

---

## 1. DDL — 그릇 다루기

### 1-1. CREATE TABLE + 제약조건

```sql
CREATE TABLE 학생 (
  학번     CHAR(5)     PRIMARY KEY,
  이름     VARCHAR(20) NOT NULL,
  학과코드  CHAR(3),
  학년     INT         DEFAULT 1 CHECK (학년 BETWEEN 1 AND 4),
  이메일   VARCHAR(50) UNIQUE,
  FOREIGN KEY (학과코드) REFERENCES 학과(학과코드)
      ON DELETE CASCADE
);
```

| 제약조건 | 뜻 | 엮이는 개념 |
|---|---|---|
| `PRIMARY KEY` | **유일 + NOT NULL** | 기본키, 개체 무결성 |
| `NOT NULL` | 비워 둘 수 없음 | — |
| `UNIQUE` | 중복 불가 (**NULL은 허용**) | 후보키 / 대체키 |
| `DEFAULT` | 값을 안 넣으면 들어갈 기본값 | INSERT 때 컬럼 생략하면 적용 |
| `CHECK` | 값의 범위·조건 제한 | 도메인 무결성 |
| `FOREIGN KEY ... REFERENCES` | 다른 테이블의 키를 참조 | 외래키, 참조 무결성 |

- **외래키 옵션**: 부모 행이 지워지거나 바뀔 때 자식 행을 어떻게 할지

| 옵션 | 부모 행 삭제 시 자식 행은 |
|---|---|
| `ON DELETE CASCADE` | 같이 삭제 |
| `ON DELETE SET NULL` | 외래키 값을 NULL로 |
| `ON DELETE RESTRICT` (또는 NO ACTION) | 자식이 있으면 부모 삭제를 **거부** |

> [!tip] 왜 PRIMARY KEY와 UNIQUE를 둘 다 두나?
> 기본키는 테이블당 **하나**뿐이고 NULL도 안 된다. 그런데 이메일처럼 "기본키는 아니지만 중복되면 안 되는" 컬럼도 있다. 그런 컬럼에 UNIQUE를 붙인다 → 키 종류의 **대체키** 자리. [[DB-기본개념]] §9-1

### 1-2. ALTER TABLE — 이미 있는 테이블의 구조 바꾸기 ⚠️

```sql
ALTER TABLE 학생 ADD 연락처 VARCHAR(20);       -- 컬럼 추가
ALTER TABLE 학생 MODIFY 연락처 VARCHAR(30);    -- 컬럼 타입·크기 변경
ALTER TABLE 학생 DROP COLUMN 연락처;           -- 컬럼 삭제
```

- `MODIFY`는 Oracle·MySQL 방식, 표준 SQL은 `ALTER COLUMN`. 둘 다 "컬럼 정의 변경"으로 기억
- 제약조건도 ALTER로 추가·삭제: `ALTER TABLE 학생 ADD CONSTRAINT ...` / `DROP CONSTRAINT ...`

> [!warning] ALTER 안에 INSERT · DELETE · UPDATE는 못 들어간다 (10-18 Q9 오답)
> 이 셋은 **행**을 다루는 DML이다. ALTER는 그릇 명령이라 그릇용 단어(**ADD / MODIFY / DROP**)만 받는다.
> 빈칸에서 "컬럼 추가 = **ADD**". INSERT가 아니다.

### 1-3. 삭제 3형제 — DROP / TRUNCATE / DELETE ⚠️

| | DROP | TRUNCATE | DELETE |
|---|---|---|---|
| 분류 | DDL | DDL | **DML** |
| 지우는 것 | 테이블 **통째로** (구조 + 데이터) | **모든 행** | **WHERE로 고른 행** |
| 구조(그릇) | ❌ 사라짐 | ✅ 남음 | ✅ 남음 |
| WHERE 사용 | ❌ | ❌ | ✅ |
| ROLLBACK | ❌ | ❌ | ✅ |
| 엑셀로 치면 | 시트 삭제 | 시트 내용 전체 지우기 | 행 골라서 삭제 |

> [!tip] 왜 TRUNCATE는 DDL인데 DELETE는 DML일까?
> DELETE는 행을 **한 줄씩** 지우고 기록(로그)을 남겨서 되돌릴 수 있다. TRUNCATE는 테이블을 **처음 만든 상태로 초기화**하는 구조 작업이라 빠르지만 되돌릴 수 없다. 결과는 비슷해 보여도 방식이 다르다.

### 1-4. RESTRICT vs CASCADE ⚠️⚠️ (10-08 · 10-18 2연속 오답)

`DROP` 뒤에 붙는 옵션. **"나를 참조하는 다른 게 있으면 어떻게 할래?"**

| 옵션 | 동작 | 말로 하면 |
|---|---|---|
| **RESTRICT** | 참조하는 객체가 있으면 **삭제 거부** | "누가 쓰고 있으니까 못 지워" |
| **CASCADE** | 참조하는 객체까지 **연쇄 삭제** | "쓰는 애들까지 다 같이 지워" |

```sql
CREATE VIEW v_영업 AS SELECT * FROM 사원 WHERE 부서 = '영업';

DROP TABLE 사원 RESTRICT;  -- ❌ 거부됨. 사원, v_영업 둘 다 그대로
DROP TABLE 사원 CASCADE;   -- ✅ 사원 + v_영업 같이 삭제
```

> [!warning] "테이블만 지우고 뷰는 남는다"는 선택지는 없다
> 뷰는 원본 테이블을 들여다보는 **창문**이다. 원본만 지우면 창문이 허공을 보게 되니 DB는 그런 상태를 만들지 않는다.
> - RESTRICT → 아예 막는다
> - CASCADE → 창문까지 치운다
>
> 10-08엔 TRUNCATE로, 10-18엔 "사원만 삭제"로 답했다.

> [!tip] CASCADE = "폭포처럼 줄줄이"
> 세 군데에서 **같은 뜻**으로 나온다.
> - `DROP ... CASCADE`: 참조하는 객체까지 삭제
> - `ON DELETE CASCADE`: 부모 행 지우면 자식 행도 삭제
> - `REVOKE ... CASCADE`: 나눠 준 권한까지 회수 (§3)

근거: 기출 2023년 2회 `DROP VIEW 학생 ( )` — "참조하는 다른 VIEW나 제약 조건까지 모두 삭제" → **CASCADE**

### 1-5. VIEW (뷰) ⚠️

- **뷰** = **SELECT문에 이름을 붙여 저장해 둔 가상 테이블**
- 데이터를 따로 갖지 않는다. 조회할 때마다 원본 테이블에서 가져온다.

```sql
CREATE VIEW v_영업 AS
  SELECT 사번, 이름 FROM 사원 WHERE 부서 = '영업';

SELECT * FROM v_영업;   -- 테이블처럼 조회 가능
DROP VIEW v_영업;       -- 뷰 삭제 (원본 사원 테이블은 그대로)
```

| 장점 | 단점 · 특징 |
|---|---|
| **논리적 독립성** (원본 구조가 바뀌어도 뷰로 보는 쪽은 영향이 적음) | **ALTER로 정의 변경 불가** → DROP 후 다시 CREATE |
| **보안** (급여 같은 민감한 열을 빼고 보여 줌) | 뷰에 **인덱스를 만들 수 없음** |
| 복잡한 쿼리를 이름 하나로 간단하게 | 뷰를 통한 INSERT·UPDATE·DELETE에 **제약** |
| 뷰 위에 또 뷰를 만들 수 있음 | 원본이 삭제되면 뷰도 못 씀 |

> [!tip] 왜 뷰는 ALTER가 안 될까?
> 뷰의 정체는 "SELECT문 한 줄"이다. 고칠 구조가 따로 없으니, 바꾸려면 그 SELECT문을 **새로 쓰는 것**(DROP → CREATE)이 자연스럽다. (일부 DB는 `CREATE OR REPLACE VIEW` 지원)

### 1-6. INDEX (인덱스) — 짧게

```sql
CREATE INDEX idx_이름 ON 학생(이름);
DROP INDEX idx_이름;
```
- 책 뒤의 **찾아보기**처럼 검색을 빠르게 한다. 대신 INSERT·UPDATE·DELETE 때마다 인덱스도 고쳐야 해서 **쓰기는 느려진다**.
- 기출 2025년 2회 단답: "인덱스(색인)"

---

## 2. DML — 내용물 다루기

### 2-1. 4형제 문법 모양 ⚠️ (빈칸 단골)

```sql
SELECT 컬럼             FROM 테이블 WHERE 조건;
INSERT INTO 테이블 (컬럼들) VALUES (값들);
UPDATE 테이블            SET 컬럼 = 새값 WHERE 조건;
DELETE                  FROM 테이블 WHERE 조건;
```

| 명령 | 꼭 붙는 짝꿍 | 외우는 법 |
|---|---|---|
| SELECT | **FROM** | 어디서(FROM) 고를래 |
| INSERT | **INTO** + **VALUES** | 안으로(INTO) 값들(VALUES)을 |
| UPDATE | **SET** | 새 값으로 세팅(SET) |
| DELETE | **FROM** | 어디서(FROM) 지울래 |

> [!warning] DELETE 뒤에는 컬럼명을 쓰지 않는다
> `DELETE 이름 FROM 학생` ❌ → `DELETE FROM 학생 WHERE ...` ✅
> DELETE는 **행 전체**를 지운다. 칸 하나만 비우려면 `UPDATE ... SET 컬럼 = NULL`.

근거: 기출 2024년 2회 — ① **VALUES** ② **SELECT** ③ **FROM** ④ **SET** 빈칸 4개가 한 문제. 2023년 1회 `delete from 학생 where ...`

### 2-2. INSERT — 행 추가

```sql
-- ① 컬럼 지정: 지정 안 한 컬럼은 NULL 또는 DEFAULT
INSERT INTO 학생 (학번, 이름) VALUES ('S001', '김');

-- ② 컬럼 생략: 테이블의 "모든 컬럼"을 "순서대로" 다 채워야 함
INSERT INTO 학생 VALUES ('S002', '이', 'C01', 2, NULL);

-- ③ SELECT 결과를 통째로 넣기 (VALUES 대신 SELECT)
INSERT INTO 졸업생 (학번, 이름)
  SELECT 학번, 이름 FROM 학생 WHERE 학년 = 4;
```

> [!tip] ③은 왜 VALUES가 없을까?
> VALUES는 "값을 직접 손으로 적을 때", 다른 테이블에서 가져올 때는 SELECT가 그 자리를 대신한다. 기출 2024-2의 ② 빈칸이 바로 이 자리(**SELECT**).

### 2-3. UPDATE — 값 고치기 ⚠️ (10-18 Q15 모름)

```sql
UPDATE 계좌 SET 잔액 = 잔액 + 500 WHERE 번호 = 2;
UPDATE 사원 SET 부서 = '퇴사', 급여 = 0 WHERE 사번 = 101;  -- 여러 컬럼은 쉼표
```

- `잔액 = 잔액 + 500` = "**지금 잔액**에 500을 더한 값으로 바꿔라" (프로그래밍의 `x = x + 500`)
- WHERE에 맞는 **모든 행**이 한꺼번에 바뀐다

> [!warning] 여러 문장은 "순서대로", 두 번째 문장은 "바뀐 표"를 본다
> ```sql
> UPDATE 계좌 SET 잔액 = 잔액 + 500 WHERE 잔액 < 1000;   -- 500 → 1000
> UPDATE 계좌 SET 잔액 = 잔액 * 2   WHERE 잔액 >= 1000;  -- 방금 1000이 된 행도 포함!
> ```
> 처음 표만 보고 두 문장을 풀면 틀린다. 문장 하나가 끝날 때마다 **표를 새로 적는다.**

> [!warning] WHERE를 빼먹으면 전부 바뀐다
> `UPDATE 사원 SET 급여 = 0;` → **전 직원** 급여 0. DELETE도 마찬가지.

### 2-4. DELETE — 행 지우기

```sql
DELETE FROM 학생 WHERE 학년 = 4;  -- 조건 맞는 행만
DELETE FROM 학생;                 -- 모든 행 (구조는 남음, ROLLBACK 가능)
```

- 외래키로 참조당하는 행을 지우면 → 외래키 옵션(CASCADE / SET NULL / RESTRICT)대로 처리 (§1-1)

### ✍️ 손으로 푸는 루틴 (UPDATE · DELETE 여러 줄 문제)

1. 테이블을 종이에 **그대로** 옮겨 적는다
2. 첫 문장의 WHERE를 **지금 표 기준으로** 한 행씩 검사 → ✔ 표시
3. ✔ 행만 고친다(지운다) → **표를 새로 한 장 더** 적는다
4. 다음 문장은 **새 표 기준**으로 2~3 반복
5. 마지막 SELECT 계산 (집계함수면 NULL 규칙 확인: [[DB-기본개념]])

---

## 3. DCL — 권한 다루기 ⚠️⚠️ (10-14 · 10-18 2연속 모름)

### 3-1. 문법 모양

```sql
GRANT  권한 ON 테이블 TO   사용자 [WITH GRANT OPTION];
REVOKE 권한 ON 테이블 FROM 사용자 [CASCADE];
```

| 포인트 | 내용 |
|---|---|
| **TO vs FROM** | 주는 건 **TO**(~에게), 뺏는 건 **FROM**(~로부터) |
| **ON** | 어떤 테이블에 대한 권한인지 |
| 권한 종류 | `SELECT`, `INSERT`, `UPDATE`, `DELETE`, `ALL` … 여러 개는 쉼표 |
| 여러 사람 | `TO kim, lee` / 모든 사용자: `TO PUBLIC` |

> [!tip] TO / FROM 헷갈리지 않는 법
> 우리말 그대로. "kim**에게** 준다(GRANT ... TO kim)", "kim**으로부터** 회수한다(REVOKE ... FROM kim)".

### 3-2. WITH GRANT OPTION — "다시 줄 수 있는 자격"

```sql
GRANT UPDATE ON 상품 TO kim WITH GRANT OPTION;   -- UPDATE 가능 + 남에게 줄 수 있음
GRANT UPDATE ON 상품 TO kim;                     -- UPDATE만 가능, 남에게는 못 줌
```

### 3-3. REVOKE와 CASCADE — 줄줄이 회수

```
DBA ──GRANT (WITH GRANT OPTION)──▶ kim ──GRANT──▶ lee

REVOKE UPDATE ON 상품 FROM kim CASCADE;
   → kim ✖, lee ✖   (kim이 나눠 준 권한까지)
```

| 명령 | kim | lee |
|---|---|---|
| `REVOKE UPDATE ON 상품 FROM kim CASCADE;` | 회수 | **회수** (연쇄) |
| `REVOKE GRANT OPTION FOR UPDATE ON 상품 FROM kim;` | UPDATE는 **유지**, 다시 줄 자격만 회수 | (DB마다 다름 → 시험엔 잘 안 나옴) |

> [!tip] 왜 연쇄 회수가 필요할까?
> lee의 권한은 **kim에게서** 나왔다. kim 권한이 사라졌는데 lee 것만 남으면 "출처 없는 권한"이 생긴다. 보안상 위험하니 줄줄이 정리한다. DROP CASCADE와 같은 발상.

### 3-4. ROLE (역할) — 짧게

```sql
CREATE ROLE 판매팀;
GRANT SELECT, INSERT ON 주문 TO 판매팀;   -- 역할에 권한을 묶고
GRANT 판매팀 TO kim, lee;                 -- 사람에게 역할을 줌
```
- 권한을 사람마다 일일이 주지 않고 **묶음(역할)**으로 관리 → 보안의 **RBAC(역할 기반 접근제어)** → [[빠르게노트-보안]]

---

## 4. TCL — 작업 묶음 확정 / 취소

| 명령 | 뜻 | ACID 연결 |
|---|---|---|
| **COMMIT** | 지금까지의 작업 **확정** (영구 저장) | 지속성 |
| **ROLLBACK** | 마지막 COMMIT 이후 작업 **전부 취소** | 원자성 (All or Nothing) |
| **SAVEPOINT** | 중간 저장 지점 → `ROLLBACK TO 지점`으로 거기까지만 취소 | — |

```sql
UPDATE 계좌 SET 잔액 = 잔액 - 1000 WHERE 번호 = 1;
SAVEPOINT s1;
UPDATE 계좌 SET 잔액 = 잔액 + 1000 WHERE 번호 = 2;
ROLLBACK TO s1;   -- 두 번째 UPDATE만 취소, 첫 번째는 살아 있음
COMMIT;           -- 첫 번째 UPDATE 확정
```

> [!warning] DDL은 ROLLBACK이 안 된다
> CREATE · ALTER · DROP · TRUNCATE는 실행하면 **자동 COMMIT**. 되돌릴 수 있는 건 **DML**(INSERT · UPDATE · DELETE)뿐이다. 삭제 3형제 중 DELETE만 ROLLBACK이 되는 이유.

> [!tip] ACID와 연결
> 계좌 이체 중 장애 → ROLLBACK으로 출금까지 취소 = **원자성**. COMMIT 후 정전돼도 유지 = **지속성**. 10-08·10-12에 헷갈렸던 그 문제들 → [[DB-기본개념]] §6

---

## 5. 시험장에서 바로 분류하는 법

```
명령어를 본다
 ├─ 테이블·컬럼·뷰·인덱스 "자체"를 만들고/바꾸고/없애나?  → DDL  (CREATE·ALTER·DROP·TRUNCATE)
 ├─ 행의 "값"을 보고/넣고/고치고/지우나?                → DML  (SELECT·INSERT·UPDATE·DELETE)
 ├─ "사람"에게 권한을 주고/뺏나?                       → DCL  (GRANT·REVOKE)
 └─ 작업을 "확정/취소"하나?                           → TCL  (COMMIT·ROLLBACK·SAVEPOINT) — 보기에 없으면 DCL
```

| 헷갈리는 쌍 | 구분 |
|---|---|
| ALTER vs UPDATE | 구조(열) vs 값(행) |
| DROP vs DELETE | 테이블 통째로(DDL) vs 행 골라서(DML) |
| TRUNCATE vs DELETE | 전부 비우기, ROLLBACK ❌ vs 골라서, ROLLBACK ✅ |
| ADD vs INSERT | 컬럼 추가(ALTER 안) vs 행 추가 |
| RESTRICT vs CASCADE | 참조 있으면 거부 vs 같이 삭제 |
| GRANT TO vs REVOKE FROM | 주기(~에게) vs 뺏기(~로부터) |

---

> [!info]- 더 알아보기 — 10-18에 같이 틀린 SELECT 문법
> 명령문은 아니지만 같은 날 틀렸거나 몰랐던 것들.
>
> | 키워드 | 정답 | 10-18 |
> |---|---|---|
> | `= NULL` vs `IS NULL` | `= NULL`은 **항상 0행** (NULL은 값이 아니라 "모름"이라 비교 불가). NULL 찾기는 **`IS NULL` / `IS NOT NULL`** | Q5 모름 |
> | `AND` / `OR` 섞임 | **NOT > AND > OR**. 괄호 없으면 AND 양쪽부터 묶기 (AND = 곱, OR = 합) | Q1 오답 |
> | `> ALL (목록)` | 모든 값보다 큼 = **`> MAX`** | Q11 모름 |
> | `> ANY (목록)` | 하나보다만 크면 됨 = **`> MIN`** · `= ANY` = **IN** | — |
> | `CROSS JOIN` | 조건 없이 **모든 조합**, 행 수 = 곱 (관계대수 교차곱 ×) · `FROM A, B`(WHERE 없음)도 같음 | Q8 모름 |
> | INNER JOIN에서 NULL | **NULL은 어떤 값과도 매칭 안 됨** → 결과에서 빠짐 | Q8 오답 |
> | `ORDER BY 반 DESC` | **DESC = 큰 것부터**, ASC = 작은 것부터(기본값). 키워드는 **바로 앞 컬럼에만** | Q12 오답 |
> | `LIKE '%S_'` | 끝에서 두 번째 글자가 S. **`%`는 0글자도 됨** → 'S2'도 포함 | Q2 오답 |
>
> 근거: 기출 2024년 1회 18번 `EMPNO > 100 AND SAL >= 3000 OR EMPNO = 200` → `(A AND B) OR C`

---

## 6. 정처기 난이도 연습문제

**문제 1.** 다음 명령어를 DDL / DML / DCL로 분류하시오.
`TRUNCATE`, `UPDATE`, `REVOKE`, `ALTER`, `SELECT`, `GRANT`, `DELETE`

> [!success]- 정답
> DDL: TRUNCATE, ALTER / DML: UPDATE, SELECT, DELETE / DCL: REVOKE, GRANT

**문제 2.** 빈칸을 채우시오.
```sql
( ① ) INTO 학생 (학번, 이름) ( ② ) ('S010', '박');
UPDATE 학생 ( ③ ) 학년 = 3 WHERE 학번 = 'S010';
( ④ ) FROM 학생 WHERE 학번 = 'S010';
```

> [!success]- 정답
> ① INSERT ② VALUES ③ SET ④ DELETE

**문제 3.** [상품] 테이블에 `할인율 INT` 컬럼을 **추가**하는 SQL을 쓰시오.

> [!success]- 정답
> `ALTER TABLE 상품 ADD 할인율 INT;`

**문제 4.** [재고] (상품, 수량): A 10, B 0, C 5
```sql
UPDATE 재고 SET 수량 = 수량 + 10 WHERE 수량 < 5;
DELETE FROM 재고 WHERE 수량 >= 10;
SELECT COUNT(*) FROM 재고;
```
결과는?

> [!success]- 정답
> **1**
> - 첫 줄: 수량 < 5인 행은 B(0)뿐 → 10. 표: A 10, B 10, C 5
> - 둘째 줄: 수량 >= 10인 A, B 삭제 → C만 남음 → 1

**문제 5.** 사용자 park에게 [주문] 테이블의 SELECT, INSERT 권한을 주되, park이 다른 사용자에게도 이 권한을 줄 수 있게 하는 SQL을 쓰시오.

> [!success]- 정답
> `GRANT SELECT, INSERT ON 주문 TO park WITH GRANT OPTION;`

**문제 6.** 학과 테이블을 참조하는 뷰 v_공대가 있다. `DROP TABLE 학과 ( ? );` 실행 시 학과와 v_공대가 함께 삭제되었다면 ?에 들어갈 옵션은?

> [!success]- 정답
> `CASCADE` (RESTRICT였다면 삭제 거부)

---

## 한 장 요약

| 분류 | 명령어 | 문법 뼈대 | 함정 |
|---|---|---|---|
| **DDL** | CREATE · ALTER · DROP · TRUNCATE | `ALTER TABLE t ADD / MODIFY / DROP COLUMN` | ALTER 안에 INSERT ❌ · RESTRICT = 거부, CASCADE = 연쇄 · 뷰는 ALTER 불가 · 자동 COMMIT |
| **DML** | SELECT · INSERT · UPDATE · DELETE | `INSERT INTO ... VALUES` · `UPDATE ... SET` · `DELETE FROM` | WHERE 없으면 전 행 · 여러 문장은 바뀐 표 기준 · DELETE 뒤 컬럼명 ❌ |
| **DCL** | GRANT · REVOKE | `GRANT ... ON ... TO` · `REVOKE ... ON ... FROM` | WITH GRANT OPTION = 재부여 자격 · REVOKE CASCADE = 연쇄 회수 |
| **TCL** | COMMIT · ROLLBACK · SAVEPOINT | `ROLLBACK TO 지점` | ROLLBACK은 DML만 · 보기에 TCL 없으면 DCL |

관련: [[SQL-명령문-카드]] (A4 요약본) · [[../02-SQL-DB/SQL-핵심]] · [[DB-기본개념]] · [[빠르게노트-보안]] · [[../concepts/02-SQL-DB]] · [[../07-일일체크/2026-10-18]]
