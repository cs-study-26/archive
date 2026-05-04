# 💡 DB Hint: 옵티마이저를 제어하는 필살기

---

- [✅ DB 힌트(Hint)란 무엇인가?](#-db-힌트hint란-무엇인가)
- [✅ 주요 힌트의 종류와 동작](#-주요-힌트의-종류와-동작)
- [✅ 쿼리 튜닝 프로세스 (Study Map)](#-쿼리-튜닝-프로세스-study-map)
- [✅ 실무 활용 상황과 주의점](#-실무-활용-상황과-주의점)

<br>

## ✅ DB 힌트(Hint)란 무엇인가?

> [!IMPORTANT]
> 옵티마이저가 항상 최선의 선택을 하지는 못합니다. 이때 개발자가 직접 **"이 인덱스를 써라"** 혹은 **"조인 순서를 이렇게 해라"**라고 명령을 내리는 것이 **힌트**입니다. 옵티마이저의 실행 계획을 강제로 변경하는 일종의 '지시어'입니다.

<br>

## ✅ 주요 힌트의 종류와 동작

### 💡 1. 인덱스 관련 힌트 (MySQL 기준)
- **USE INDEX:** 특정 인덱스를 사용하도록 권장합니다.
  [Example: SELECT * FROM members USE INDEX (idx_email) WHERE email = 'test@...';]
- **FORCE INDEX:** USE INDEX보다 강력하게 특정 인덱스 사용을 강제합니다. 옵티마이저가 풀 스캔이 낫다고 판단해도 무시합니다.
  [Example: SELECT * FROM members FORCE INDEX (idx_email) WHERE email = 'test@...';]
- **IGNORE INDEX:** 특정 인덱스를 사용하지 못하게 배제합니다.

### 💡 2. 조인 순서 관련 힌트
- **STRAIGHT_JOIN (MySQL):** FROM 절에 나열된 순서대로 테이블을 조인하도록 강제합니다.
  [Example: SELECT * FROM table1 STRAIGHT_JOIN table2 WHERE ...;]
- **LEADING (Oracle 스타일):** 조인 시 먼저 읽어야 할 드라이빙 테이블을 지정합니다.
  [Example: /*+ LEADING(table2 table1) */]

### 💡 3. 조인 방식 관련 힌트 (Oracle/PostgreSQL 중심)
- **USE_NL:** 중첩 루프 조인(Nested Loop Join)을 유도합니다.
- **USE_HASH:** 해시 조인(Hash Join)을 유도합니다.

<br>

## ✅ 쿼리 튜닝 프로세스 (Study Map)

---

성능 문제가 발생했을 때 차근차근 따라가야 할 로드맵입니다.

1. **Slow Query 수집:** DB 로그를 통해 응답 시간이 긴 쿼리 추출
2. **실행 계획 분석:** EXPLAIN으로 인덱스 활용 여부와 스캔 방식 확인
3. **인덱스 리빌딩:** 데이터가 급변했다면 통계 정보 갱신(ANALYZE)
4. **인덱스 재설계:** 카디널리티를 고려한 복합 인덱스 생성
5. **쿼리 리팩토링:** 컬럼 가공 제거 및 서브쿼리를 조인으로 변경
6. **최후의 수단:** 힌트(Hint) 적용으로 경로 강제

<br>
---

## ✅ 실무 활용 상황과 주의점

### 💡 언제 쓰는가?
1. **통계 정보 불일치:** 데이터 변화가 심해 옵티마이저가 엉뚱한 인덱스를 잡을 때.
2. **복잡한 조인:** 테이블이 5~10개 이상 조인되어 옵티마이저가 경우의 수를 다 계산하지 못하고 적당한 계획을 내놓았을 때.
3. **특정 데이터 편향:** 특정 조건의 데이터만 유독 많아서 일반적인 인덱스 전략이 안 통할 때.

### ⚠️ 주의점 (Maintenance Risk)
- **경직성:** 데이터 분포가 바뀌어 힌트가 지시한 경로가 더 이상 최적이 아니게 되어도, DB는 시키는 대로만 합니다. 이는 나중에 심각한 성능 저하의 원인이 됩니다.
- **코드 관리:** 쿼리 곳곳에 힌트가 박혀 있으면 DB 버전 업그레이드나 구조 변경 시 관리 포인트가 늘어납니다.


**출처**
- [MySQL 공식 가이드: Optimizer 및 EXPLAIN](https://dev.mysql.com/doc/refman/8.0/en/explain.html)
- [Real MySQL 8.0 (백은빈 저)](https://wikibook.co.kr/real-mysql-80-1/)
- [데이터베이스 성능 튜닝의 기초 (SQLP 가이드 참조)](https://www.dbguide.net/)