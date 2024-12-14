# SQL 기초 4 - 트랜잭션, 기타 고급 문법

[Velog](https://velog.io/@semoon/SQL-기초-4-트랜잭션-기타-고급-문법)<br>
# 트랜잭션

**Atomic하게 실행**되어야 하는 쿼리를 묶어 **하나의 작업처럼 처리**하는 방법

- Ex. 은행 계좌이체 → 인출 + 입금 으로 구성 → 둘 다 성공하든지 둘 다 실패해야 함
    
    ```sql
    BEGIN;
    	A 계좌에서 인출;
    	B 계좌로 입금;
    END;
    ```
    
- 레코드 수정/추가/삭제 등에만 의미가 있음
- 조회는 데이터가 변하지 않아 의미가 없음

- `BEGIN - END` 혹은 `BEGIN - COMMIT` 사이에 묶을 쿼리들을 작성
- `ROLLBACK` 을 통해서 `BEGIN` 이전의 상태로 돌릴 수 있음
- 이는 commit mode에 따라 달라짐
    - `autocommit = True`
        - 모든 레코드 수정/삭제/추가 작업이 **자동 커밋**
        - 특정 작업을 트랜잭션으로 묶고 싶을 때 `BEGIN - END(COMMIT)/ROLLBACK` 사용 가능
    - `autocommit = False`
        - 모든 레코드 수정/삭제/추가 작업이 **`COMMIT` 이 호출될 때까지 커밋되지 않음**

# 기타 고급 SQL 문법

## UNION, EXCEPT, INTERSECT

- `UNION` (합집합)
    - 여러 테이블 또는 SELECT 결과를 **중복을 제거하여** 하나의 결과로 합쳐줌
    - `UNION ALL` : 중복을 제거하지 않음
    - 필드 수와 타입이 일치하는 경우만 수행 가능
- `EXCEPT` (차집합)
    - 하나의 SELECT 결과에서 다른 SELECT 결과를 뺌
    - 필드 수와 타입이 일치하는 경우만 수행 가능
    - 새로운 SELECT문을 기존의 SELECT문과 비교하여 테스트할 때 사용 가능
- `INTERSECT` (교집합)
    - 여러 SELECT 문에서 같은 레코드들만 찾아줌
    - 새로운 SELECT문을 기존의 SELECT문과 비교하여 테스트할 때 사용 가능

## COALESCE, NULLIF

- `COALESCE`
    
    ```sql
    COALESCE(exp1, exp2, exp3, ..., default=NULL)
    ```
    
    - exp를 앞부터 비교하여 NULL이 아니면 해당 값을 리턴하고, NULL이면 다음 exp를 비교
    - 모두 NULL이면 default값을 리턴하는데, 이를 설정하지 않으면 NULL을 리턴
    - NULL 값을 다른 값으로 바꾸고 싶을 때 사용
- `NULLIF`
    
    ```sql
    NULLIF(exp1, exp2)
    ```
    
    - exp1과 exp2의 값이 **같으면 NULL**을 리턴, **다르면 exp1의 값** 리턴
    - divide-by-zero 에러를 피하기 위해 사용 가능

## LISTAGG

```sql
LISTAGG(column1, [delimiter]) WITHIN GROUP (ORDER BY order_column)
```

- 그룹별로 값을 리스트함
- `WITHIN GROUP (ORDER BY order_column)` 부분을 통해서 순서를 부여하여 리스트 가능
- 집계함수 중 하나
- 리스트한다?
    - 함수의 인풋으로 넣은 컬럼이 가진 값을 정렬한 순서대로 **하나의 문자열로 이어붙인 값을** 리턴
    - delimiter 지정을 통해 리스트할 때 구분자를 넣을 수 있음
- `WITHIN` ?
    - 집계 함수에서 정렬된 순서를 기준으로 연산할 때 사용
    - **결과 집합 전체가 아니라 집계 함수의 내부 값에만 적용 (ORDER BY와의 차이점)**
    - LISTAGG 외 사용 가능 함수
        - `PERCENTILE_CONT` : 연속형 데이터 백분위수 계산 → 중앙값 계산시 WITHIN 사용
        - `PERCENTILE_DISC` : 이산형 데이터 백분위수 계산 → 중앙값 계산시 WITHIN 사용
        - `MODE` : 최빈값 계산

## WINDOW

```sql
function(expression) OVER ([PARTITION BY expression] [ORDER BY expression])
```

- 정해진 영역(윈도우, 그룹) 내에서  정렬하여 값을 계산하여 리턴
- 모든 레코드를 유지하며 값을 반환
- 유용한 윈도우 함수
    - `ROW_NUMBER`, `FIRST_VALUE`, `LAST_VALUE`, `LAG`
    - 수학 함수: `AVG`, `SUM`, `COUNT`, `MAX`, `MIN`, `MEDIAN`, `NTH_VALUE`

## LAG

```sql
LAG(column, n=1) OVER (PARTITION BY partition_column ORDER BY order_column)
```

- n번째 전 레코드의 값을 가져와 리턴함
- 역순으로 정렬하여 n번째 후 레코드의 값을 가져오도록 할 수 있음 (응용)

## JSON Parsing 함수

- `JSON_EXTRACT_PATH_TEXT`
    
    ```sql
    JSON_EXTRACT_PATH_TEXT(json_string, key1, key2, ...)
    ```
    
    - JSON String을 입력으로 받아 특정 필드의 값을 추출 가능 (nested 구조 지원)
    - JSON의 포맷을 이미 아는 상황에서만 사용 가능
