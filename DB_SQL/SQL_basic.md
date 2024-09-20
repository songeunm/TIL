## SQL 기본

- 다수의 SQL문은 세미콜론으로 구분
- `-`: 한 줄 주석
- `/*` `/`: 여러 줄 주석
- SQL 키워드는 포맷팅 필요 (대문자 등)
- 테이블·필드 이름의 명명규칙 필요 (단수형/복수형, 언더바/대문자)

## DDL

테이블 구조 정의 언어

- 테이블 생성
    - `CREATE TABLE`
        
        PK 지정 가능
        
        DW에서는 PK uniqueness가 지켜지지 않음
        
        ```sql
        CREATE TABLE raw_data.table_1 (
        	column_1 int,
        	column_2 varchar(32) primary key,
        	column_3 varchar(32)
        );
        ```
        
    - `CTAS`
        
        `CREATE TABLE schema_name.table_name AS SELECT` 의 약자
        
        SELECT문을 이용하여 간단하게 테이블을 생성과 동시에 내용을 채울 수 있다.
        
        ```sql
        CREATE TABLE raw_data.table_2 AS
        SELECT column_1, column_2
        FROM raw_data.table_1;
        ```
        
- 테이블 삭제
    - `DROP TABLE`
        
        존재하지 않는 테이블 삭제시 에러 발생 → `IF EXISTS` 를 뒤에 추가해 해결 가능
        
    - vs. `DELETE FROM`
        
        `DROP TABLE` 은 DDL로 테이블의 존재를 삭제
        `DELETE FROM` 은 DML로 테이블의 레코드를 삭제 (조건 삭제 가능)
        
- 테이블 구조 변경
    - `ALTER TABLE`
        - 새로운 컬럼 추가
            
            ```sql
            ALTER TABLE table_name ADD COLUMN field_name field_type; 
            ```
            
        - 컬럼 이름 변경
            
            ```sql
            ALTER TABLE table_name RENAME field_name TO new_field_name; 
            ```
            
        - 컬럼 삭제
            
            ```sql
            ALTER TABLE table_name DROP COLUMN field_name; 
            ```
            
        - 테이블 이름 변경
            
            ```sql
            ALTER TABLE table_name RENAME TO new_table_name;
            ```
            

## DML

테이블 데이터 조작 언어

- 레코드 질의
    - `SELECT column_1 FROM table_name`
        
        테이블에서 레코드, 필드를 읽어옴
        
    - `WHERE`
        
        레코드 선택 조건을 지정
        
    - `GROUP BY`
        
        그룹별 집계
        
    - `ORDER BY`
        
        레코드를 정렬
        
    - 다수의 테이블을 조인해서 사용하기도 한다.
    
    ```sql
    SELECT COUNT(column_1), column_2
    FROM raw_data.table1
    WHERE column_1 >= 3
    GROUP BY column_2
    HAVING COUNT(column_1) > 0
    ORDER BY 1 DESC
    LIMIT 5;
    ```
    
- 레코드 수정
    - `INSERT INTO`<br>
        
        레코드를 추가
        
    - `UPDATE FROM`<br>
        
        레코드의 필드 값 수정
        
    - `DELETE FROM`<br>
        
        레코드를 삭제
        
        - vs. `TRUNCATE`<br>
            
            `DELETE FROM` 은 조건 지정이 가능, 속도 느림
            `TRUNCATE` 는 조건 지정 불가능, 모든 레코드 삭제, 속도 빠름
            

## 데이터를 다룰 때 주의할 점

- 현업에 깨끗한 데이터는 없다.
    - 데이터를 믿을 수 있는지 의심할 것
    - 레코드를 직접 살펴볼 것
- 항상 데이터의 품질을 체크해야한다.
    - **중복**된 레코드가 있는가?
    - **최근** 데이터가 있는가? (freshness)
    - **PK uniqueness**가 지켜지는가?
    - **값이 비어있는** 컬럼이 있는가?
    
    **⇒ unit test의 형태로 만들어 매번 쉽게 체크 가능**
    
- 중요한 테이블이 무엇인지 알고 그 메타정보를 잘 관리하는 것이 중요하다.

## 문법 세부 관찰

### SELECT

테이블에서 레코드를 읽어온다.

- `WHERE`<br>
    
    레코드 선택 조건을 지정
    
    컬럼 이름 대신 `*` 를 넣으면 모든 컬럼을 선택한다.
    
- `LIMIT`<br>
    
    레코드의 수를 제한
    
    `ORDER BY` 와 자주 함께 사용된다.
    
- `DISTINCT`<br>
    
    레코드 중복 제거
    
    SELECT에서 선택한 컬럼 이름 앞에 붙여 해당 컬럼의 중복을 제거한다.
    
- 집계함수<br>
    
    COUNT, SUM, AVG, MAX, MIN 등
    
    `GROUP BY` 와 함께 사용된다.
    
    NULL은 집계되지 않는다.
    
- `CASE WHEN`<br>
    
    필드값을 조건에 따라 변환
    
    ```sql
    CASE
    	WHEN 조건1 THEN 값1
    	WHEN 조건2 THEN 값2
    	ELSE 값3
    END AS 필드이름
    ```
    
- `NULL`<br>
    
    값이 존재하지 않음을 나타내는 상수
    
    0, “”과 다름
    
    - NULL인지 아닌지 확인은 `is (not) NULL` 을 사용한다. (등호 사용 X)
        
        ```sql
        field1 is NULL
        field1 is not NULL
        ```
        
    - NULL이 사칙연산에 사용되면 모두 NULL이 된다.

### COUNT

조건을 만족하는 레코드의 수 반환

NULL은 카운트되지 않음

- ex)
    - `SELECT COUNT(1) FROM count_test`<br>
        
        각각의 레코드에서 1을 받음 → 7
        
    - `SELECT COUNT(0) FROM count_test`<br>
        
        각각의 레코드에서 0을 받음 → 7
        
    - `SELECT COUNT(NULL) FROM count_test`<br>
        
        각각의 레코드에서 NULL을 받음 → 0
        
    - `SELECT COUNT(value) FROM count_test`<br>
        
        각각의 레코드에서 value 값을 받음 → 6
        
    - `SELECT COUNT(DISTINCT value) FROM count_test`<br>
        
        각각의 레코드에서 중복을 제거한 value 값을 받음 → 4
      
  <img src=https://velog.velcdn.com/images/semoon/post/c4817955-60f8-4e72-96cd-2d0f7aace6fd/image.png width=20%>
    ⇒ 어떤 값을 세는지에 주의
    


### WHERE

레코드 선택 조건 지정

- `IN` · `NOT IN`<br>
    
    값이 어떤 값들 집합에 포함되는지(포함되지 않는지) 확인
    
    ```sql
    WHERE channel IN ('Google', 'Youtube')
    WHERE channel = 'Google' OR channel = 'Youtube'
    ```
    
- `LIKE` , `ILIKE`<br>
    
    문자열이 어떤 포맷에 맞는지 확인
    
    `LIKE` 는 대소문자를 구별하여 매칭
    `ILIKE` 는 대소문자를 구별하지 않고 매칭
    
    `NOT`을 앞에 붙일 수 있다.
    
    ```sql
    WHERE channel LIKE 'G%'
    WHERE channel LIKE '%o%'
    ```
    
- `BETWEEN`<br>
    
    DATE 타입의 경우 특정 기한에 해당하는지 확인
    
- STRING Functions
    - `LEFT(str, N)`<br>
        
        str에서 처음을 1번째로 N번째 문자를 반환
        
    - `REPLACE(str, exp1, exp2)`<br>
        
        str에서 exp1을 찾아 exp2로 변환
        
    - `UPPER(str)`<br>
        
        str을 전부 대문자로 변환
        
    - `LOWER(str)`<br>
        
        str을 전부 소문자로 변환
        
    - `LEN(str)`<br>
        
        str의 길이 반환
        
    - `LPAD(str, N, exp)` , `RPAD(str, N, exp)`<br>
        
        str의 길이가 N보다 작으면 왼쪽·오른쪽에 exp를 채워 패딩을 추가
        
    - `SUBSTRING(str, N1, N2)`<br>
        
        str에서 처음을 1번째로 N1번째부터 N2개의 문자를 반환
        

### ORDER BY

레코드를 특정 컬럼을 기준으로 정렬

- 기준 컬럼을 여러개 지정할 수 있다.
- SELECT 에 명시된 컬럼의 순서를 사용할 수 있다. (1부터 시작)
- `ASC` 는 오름차순(default), `DESC` 는 내림차순<br>
    
    `ASC` / `DESC` 는 컬럼 뒤에 붙여준다.
    
- **NULL값은 가장 큰 값으로 인식**하여 정렬<br>
    
    변경하고싶다면 `NULL FIRST` / `NULL LAST` 를 통해 지정이 가능하다.
    

### 타입 변환

- DATE Conversion
    - 타임존 변환<br>
        
        `CONVERT_TIMEZONE('America/Los_Angeles', ts)`
        
    - `DATE_TRUNC(datepart, time_column)`<br>
        
        datepart까지만 표현하도록 변환<br>
        
        datepart은 second, minute, hour, day, week, month, quarter, year, decade 등 가능
        
    - `EXTRACT(datepart from time_column)` , `DATE_PART(interval, time_column)`<br>
        
        datepart만을 추출<br>
        
        `EXTRACT` 는 Oracle<br>
        
        `DATE_PART` 는 PostgreSQL
        
    - `DATEDIFF(seperator, start_time_column, end_time_column)`<br>
        
        start_time_column과 end_time_column의 차이를 seperator를 기준으로 계산<br>
        
        seperator는 year(yy), quarter(qq), month(mm), day(dd), week(wk), hour(m), minute(n), secod(s) 등 가능
        
    - `DATEADD(datepart, N, time_column)`<br>
        
        time_column에서 N만큼의 datepart를 더해 반환
        
    - `GET_CURRENT`<br>
        
        현재 시각을 반환
        
- `TO_CHAR` / `TO_TIMESTAMP`<br>
    
    숫자나 시간을 문자열로, 문자열을 날짜시간 타입으로 변경
    
- Type Casting
    - `column_name::type`
    - `CAST(column_name as type)`