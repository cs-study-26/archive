# 🧑🏻‍💻 Index Optimization

---

- [✅ 옵티마이저와 실행 계획](#-옵티마이저와-실행-계획)
- [✅ 복합 인덱스 (Composite Index) 설계](#-복합-인덱스-composite-index-설계)
- [✅ 커버링 인덱스 (Covering Index)](#-커버링-인덱스-covering-index)
- [✅ 인덱스가 타지 않는 상황 (Anti-Patterns)](#-인덱스가-타지-않는-상황-anti-patterns)

<br>

## ✅ 옵티마이저와 실행 계획

---

> [!NOTE]
> **옵티마이저(Optimizer)**는 SQL을 가장 효율적으로 실행할 경로(실행 계획)를 결정하는 엔진이다.
> 인덱스가 있어도 옵티마이저가 판단하기에 "전체 테이블을 읽는 게 더 싸겠다" 싶으면 인덱스를 타지 않는다.

### 💡 실행 계획 확인 (EXPLAIN)
쿼리 앞에 `EXPLAIN`을 붙여 옵티마이저의 판단을 확인한다.
- **type:** `const`, `ref`, `range` 등이면 좋고, `ALL`이면 인덱스를 안 타는 풀 스캔이다.
- **key:** 실제로 사용된 인덱스 이름.
- **Extra:** `Using index`가 나오면 **커버링 인덱스**로 동작한 것이다.

<br>

## ✅ 복합 인덱스 (Composite Index) 설계

---

두 개 이상의 컬럼을 묶어 하나의 인덱스로 만드는 것이다. 이때 **컬럼의 순서**가 절대적이다.

- **규칙:** 첫 번째 컬럼이 조건절(`WHERE`)에 반드시 포함되어야 한다.
- **순서 결정 팁:** 1. `=` 조건으로 자주 쓰이는 컬럼을 앞쪽에 배치한다.
  2. 카디널리티가 높은 컬럼을 앞쪽에 배치한다.
  3. 범위 조건(`<`, `>`, `BETWEEN`)은 뒤쪽으로 배치한다.

```sql
-- (A, B) 순서의 인덱스일 때
WHERE A = 1 AND B = 2; -- (O) 활용
WHERE A = 1;           -- (O) 활용
WHERE B = 2;           -- (X) 활용 불가 (A가 없기 때문)
```

## ✅ 커버링 인덱스 (Covering Index)
---
> [!TIP]
"인덱스만 읽고 쿼리를 끝내는 최강의 성능 최적화"
>- 쿼리에 필요한 **모든 컬럼(SELECT, WHERE, ORDER BY 등)**이 인덱스에 다 포함되어 있는 상태다.
>- 실제 데이터 블록에 접근(Random I/O)할 필요가 없으므로 성능이 매우 빠르다.

## ✅ 인덱스가 타지 않는 상황 (Anti-Patterns)
인덱스를 만들어도 다음의 경우 옵티마이저는 인덱스를 무시한다.

1. 인덱스 컬럼을 가공할 경우
    ```sql
    select * from table where LOWER(name)  ='word';
    select * from table where idx - 1 = 5;
    ```
2. 암시적 형변환: 문자열 컬럼을 숫자값으로 비교할 경우
    ```sql
    select * from table where age = '30'
    ```
3. NOT 또는 IN 연산자 사용할 경우
    ```sql
    select * from employees where name != 'John';
    select * from employees where name in ('John', 'Kevin');
    ```
4. LIKE 앞 부분에 와일드카드(`%`)를 사용할 경우
    ```sql
    select * from table where name like '%word';
    ```
    - 뒷 부분에 사용할 경우는 인덱스 사용 가능
        ```sql
        select * from table where name like 'word%';
        ```
5. 복합 컬럼 INDEX에서 순서가 잘못된 경우 - 공통적으로 사용된 조건절의 컬럼을 
인덱스 선행 컬럼으로 사용
    ```sql
    create index idx_employee_name_age on employees (name, age);
    select * from table where age
    ```
6. OR 조건의 경우 - OR 조건에 걸린 두개의 컬럼 모두 INDEX가 있어도, 최적의 OR 조건을 뽑기 힘들어 FULL SCAN 처리
    ```sql
    select * from table where name = 'word' or idx = 5
    ```
7. 옵티마이저의 선택 - 인덱스가 두개 다 있을 경우, 하나만 선택될 수도 있고, 둘 다 선택 될 수도 있음
    ```sql
    select * from table where name = 'word' and id = 'elky';
    ```

**출처** 
- [1. 커버링 인덱스 (기본 지식 / WHERE / GROUP BY)](https://jojoldu.tistory.com/476)
- [DB 인덱스를 사용하지 못하는 케이스](https://velog.io/@usa_dev/DB-%EC%9D%B8%EB%8D%B1%EC%8A%A4%EB%A5%BC-%EC%82%AC%EC%9A%A9%ED%95%98%EC%A7%80-%EB%AA%BB%ED%95%98%EB%8A%94-%EC%BC%80%EC%9D%B4%EC%8A%A4)