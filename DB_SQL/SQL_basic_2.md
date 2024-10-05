# SQL 기초 2

[Velog](https://velog.io/@semoon/SQL-%EA%B8%B0%EC%B4%88-2)

### GROUP BY  & Aggregate 함수

레코드를 그룹핑하여 그룹별로 다양한 정보 계산

- GROUP BY를 이용하여 그룹핑
    - 필드 이름, 필드 일련번호 사용 가능
- 그룹별로 계산할 내용 결정
    - COUNT, SUM, AVG, MIN, MAX, LISTAGG 등
    - 계산 결과가 새로운 필드가 되고, 이 필드에 필드 이름 지정하는 것이 일반적
- `AS`
    - Alias를 지정할 때 사용 (필드/테이블 이름에 새로운 이름을 지정)
    - Alias 지정에 AS 생략 가능
    
    ```sql
    SELECT  column_1,
            MIN(column_2) AS calc_1,
            MAX(column_2) calc_2
    FROM table AS A
    GROUP BY 1;
    ```
    

### CTAS

SELECT를 통해서 간단하게 테이블을 생성하는 방법

Create Table As Select

- 자주 조인하는 테이블을 CTAS를 통해 조인해두면 편리, 효율 상승

```sql
-- 테이블 생성 전 삭제
DROP TABLE IF EXISTS adhoc.table_3;
-- CTAS
CREATE TABLE adhoc.table_3 AS
SELECT A.*, B.*
FROM raw_data.table_1 A JOIN raw_data.table_2 B
ON A.join_key = B.join_key;
```

### CTE (inline view)

**하나의 쿼리문이 끝날 때까지만 지속되는 일회성 테이블**

Common Table Expression

- `WITH`를 사용해서 임시 테이블을 생성
- 권한 필요 X
- 메모리에서만 존재
- 가독성과 재사용성을 위해 파생테이블 대신 사용하기에 유용
- MySQL 8과 PostgreSQL에서는 cache처럼 임시저장됨 (Materializing) → 무분별하게 사용시 오히려 성능 저하
- Query Optimizer가 execution plan 계산시 CTE의 최적화 고려 X (ex. CTE에 index 적용 X)

```sql
WITH cte_table AS(
SELECT *
FROM table
)
```

### VIEW

쿼리문의 Alias

- 쿼리의 결과를 저장 X → 쿼리를 저장
- 다른 쿼리문에서 호출할 때마다 쿼리가 실행
- 물리적 객체로 디스크에 저장

```sql
CREATE VIEW view_name AS
SELECT *
FROM table
```
