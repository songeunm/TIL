# SQL 기초 3

[Velog](https://velog.io/@semoon/SQL-%EA%B8%B0%EC%B4%88-3)<br>
## JOIN

두 개 혹은 그 이상의 테이블들을 공통 필드를 중심으로 머지

- 스타 스키마로 구성된 테이블들로 분산되어 있던 정보를 통합하는데 사용

```sql
-- JOIN example
SELECT A.*, B.*
FROM raw_data.table1 A
JOIN raw_data.table2 B ON A.key1 = B.key1 and A.key2 = B.key2
WHERE A.ts >= '2024-11-19';
```

- JOIN시 고려해야할 점
    - **중복 레코드가 없고 PK의 uniqueness가 보장됨을 체크**
    - 조인하는 **테이블 간의 관계**를 명확하게 정의 (ex. 1:1, 1:n, n:1, n:m)
    - 어느 테이블을 베이스로 잡을지 결정 (FROM에 들어갈 테이블)
    
    ⇒ 복잡한 JOIN시 JOIN 전략부터 수립!
    

## JOIN의 종류

- INNER JOIN
    - 양쪽 테이블에서 매치가 되는 레코드만 리턴
    - 매치되는 레코드만 리턴하기 때문에 모든 필드가 채워진 상태로 리턴
- LEFT JOIN
    - 왼쪽 테이블(Base)의 모든 레코드를 리턴
    - 오른쪽 테이블의 필드는 왼쪽 레코드와 매칭되는 경우만 채워진 상태로 리턴
- FULL JOIN
    - 왼쪽 테이블과 오른쪽 테이블의 모든 레코드를 리턴
    - 매칭되는 경우에만 양쪽 테이블의 모든 필드가 채워진 상태로 리턴
- CROSS JOIN
    - 왼쪽 테이블과 오른쪽 테이블의 모든 레코드들의 **조합**을 리턴
- SELF JOIN
    - 동일한 테이블을 alias를 달리해 inner 조인

## BOOLEAN 처리

True나 False를 나타내는 타입

- 사용 예시
    - `flag = True`
    - `flag is True`
    - `flag is not True`  (≠ `flag is False`  null 값을 주의)

## NULL 비교 및 처리

비교에 `is` , `is not` 사용!! (= <> 사용 불가)

- 사용 예시
    - `flag is NULL`
    - `flag is not NULL`
- NULLIF
    - 필드의 값을 조건에 맞춰 NULL값으로 변경하는 함수
    - `NULLIF(value, 0)`
- COALESCE
    - NULL 대신 다른 백업값을 리턴하는 함수
    - `COALESCE(exp1, exp2, exp3, ... )`

## ROW_NUMBER

새로운 컬럼을 생성하여 한 컬럼의 값 별로 레코드들을 넘버링하는 함수

`ROW_NUMBER() OVER (PARTITION BY field1 ORDER BY field2) nn`
