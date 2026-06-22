---
name: SQL-핵심
description: 정보처리기사 실기 SQL 핵심 — 매 회차 2~3문제 출제
source_pdf: 2026_정보처리기사_실기_기출문제집_핵심요약(20260223).pdf
part: 데이터베이스
keywords: [SQL, SELECT, JOIN, GROUP BY, HAVING, 서브쿼리, INSERT, UPDATE, DELETE]
tags: [#sql, #database, #priority-1, #concept]
---

# SQL 핵심

> [!important] 출제 빈도 ★★★★★
> 매 회차 2~3문제. SELECT 결과 도출, SQL 작성, DML 구문 완성

---

## DML (Data Manipulation Language)

### SELECT 기본 구조

```sql
SELECT [DISTINCT] 컬럼명
FROM 테이블명
[WHERE 조건]
[GROUP BY 컬럼명]
[HAVING 그룹조건]
[ORDER BY 컬럼명 [ASC|DESC]];
```

> [!tip] WHERE vs HAVING
> - `WHERE`: 그룹화 **이전** 행 조건 (집계함수 사용 ❌)
> - `HAVING`: 그룹화 **이후** 그룹 조건 (집계함수 사용 ✅)

### INSERT

```sql
-- 값 직접 입력
INSERT INTO 테이블(컬럼1, 컬럼2) VALUES (값1, 값2);

-- 서브쿼리로 입력
INSERT INTO 테이블(컬럼1, 컬럼2)
SELECT 컬럼1, 컬럼2 FROM 다른테이블 WHERE 조건;
```

> [!example] 기출 (2024년 2회차 3번)
> ```sql
> INSERT INTO 사원 (사원번호, 이름) VALUES (32431, '정실기');  -- ① VALUES
> INSERT INTO 부서 SELECT 사원번호, 이름 FROM 사원 WHERE ...;  -- ② SELECT
> ```

### UPDATE

```sql
UPDATE 테이블
SET 컬럼 = 값
WHERE 조건;
```

### DELETE

```sql
DELETE FROM 테이블 WHERE 조건;
-- 예: DELETE FROM 학생 WHERE 이름 = '민수';
```

---

## JOIN 종류

```
JOIN 종류
├── INNER JOIN   — 양쪽에 모두 있는 행만
├── LEFT OUTER JOIN  — 왼쪽 테이블 전체 + 오른쪽 매칭
├── RIGHT OUTER JOIN — 오른쪽 테이블 전체 + 왼쪽 매칭
├── FULL OUTER JOIN  — 양쪽 모두
└── CROSS JOIN   — 카테시안 곱 (모든 조합)
```

### JOIN 예시 (기출 패턴)

```sql
-- 2026년 1회차 18번 패턴
SELECT COUNT(*)
FROM employee e
JOIN dept d ON e.dep_id = d.dept_id
WHERE d.budget > (SELECT AVG(budget) FROM dept);
```

---

## 집계 함수

| 함수 | 설명 |
|------|------|
| `COUNT(*)` | 전체 행 수 (NULL 포함) |
| `COUNT(컬럼)` | NULL 제외 행 수 |
| `COUNT(DISTINCT 컬럼)` | 중복 제거 후 개수 |
| `SUM(컬럼)` | 합계 |
| `AVG(컬럼)` | 평균 |
| `MAX(컬럼)` | 최댓값 |
| `MIN(컬럼)` | 최솟값 |

---

## DISTINCT

```sql
SELECT DEPT FROM STUDENT;           -- 200행 (전체)
SELECT DISTINCT DEPT FROM STUDENT;  -- 3행 (중복 제거)
SELECT COUNT(DISTINCT DEPT) FROM STUDENT WHERE DEPT='컴퓨터과'; -- 1
```

> [!example] 기출 (2026년 1회차 9번)
> STUDENT 테이블: 컴퓨터과 50명, 인터넷과 100명, 사무자동화과 50명
> - `SELECT DEPT` → 200행
> - `SELECT DISTINCT DEPT` → 3행
> - `COUNT(DISTINCT DEPT) WHERE DEPT='컴퓨터과'` → 1

---

## 서브쿼리 (Subquery)

```sql
-- IN 서브쿼리
SELECT B FROM R1
WHERE C IN (SELECT C FROM R2 WHERE D='k');

-- 집계 서브쿼리
SELECT * FROM emp
WHERE salary > (SELECT AVG(salary) FROM emp);
```

---

## GROUP BY + HAVING

```sql
-- 과목별 평균 90점 이상인 과목만 조회
SELECT 과목이름, MIN(점수) AS 최소점수, MAX(점수) AS 최대점수
FROM 성적
GROUP BY 과목이름
HAVING AVG(점수) >= 90;
```

> [!example] 기출 (2023년 1회차 16번)
> WHERE 절 사용 금지 조건 → GROUP BY + HAVING 필수

---

## DDL (Data Definition Language)

### CREATE TABLE + 제약조건

```sql
CREATE TABLE PLAYER (
    PLAYER_ID  CHAR(7)     NOT NULL,
    PLAYER_NAME VARCHAR2(20) NOT NULL,
    TEAM_ID   CHAR(3)     NOT NULL,
    PRIMARY KEY (PLAYER_ID),
    CONSTRAINT TEAM_TF           -- ① CONSTRAINT 이름
    FOREIGN KEY (TEAM_ID)        -- ② FOREIGN KEY 컬럼
    REFERENCES TEAM (TEAM_ID2)   -- ③ REFERENCES 대상테이블(컬럼)
);
```

> [!example] 기출 (2026년 1회차 10번)
> ① CONSTRAINT ② FOREIGN ③ TEAM_ID ④ REFERENCES ⑤ TEAM_ID2

### DROP VIEW + CASCADE

```sql
DROP VIEW 학생 CASCADE;
-- CASCADE: 참조하는 다른 VIEW나 제약조건까지 모두 삭제
```

---

## 관계 대수 기호

| 연산 | 기호 | 의미 |
|------|------|------|
| Select (선택) | **σ** | 조건에 맞는 행 선택 |
| Project (프로젝션) | **π** | 지정한 열만 추출 |
| Join (조인) | **⋈** | 두 릴레이션 결합 |
| Division (나누기) | **÷** | 제수의 모든 속성과 매칭 |

> [!example] 기출 (2023년 2회차 19번)
> `πTTL(employee)` → employee 테이블에서 TTL 열만 추출

---

## SQL 조인 유형 (관계대수)

| 조인 유형 | 설명 |
|-----------|------|
| **세타 조인(θ-join)** | 임의의 비교 연산자로 조인 |
| **동등 조인(Equi-join)** | `=` 연산자로 조인 (중복 속성 포함) |
| **자연 조인(Natural join)** | 동등 조인에서 중복 속성 제거 |

---

## 반정규화 (Denormalization)

- 성능 향상을 위해 **데이터를 중복 저장** 또는 테이블 합침
- 데이터 무결성이 저하될 수 있음
- 정규화의 반대 개념

[[SQL-연습문제]] | [[DB-개념]] | [[정규화]]
