## SQL 기본

- 다수의 SQL문은 세미콜론으로 구분
- `-`: 한 줄 주석
- `/*` `/`: 여러 줄 주석
- SQL 키워드는 포맷팅 필요 (대문자 등)
- 테이블·필드 이름의 명명규칙 필요 (단수형/복수형, 언더바/대문자)

## DDL

테이블 구조 정의 언어

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
    
    SELECT문을 이용하여 간단하게 테이블을 생성과 동시에 내용을 채울 수 있음
    
    ```sql
    CREATE TABLE raw_data.table_2 AS
    SELECT column_1, column_2
    FROM raw_data.table_1;
    ```
    
- `DROP TABLE`
    
    테이블을 삭제
    
    존재하지 않는 테이블 삭제시 에러 발생
    
    ↳ `IF EXISTS` 를 뒤에 추가해 해결 가능
    
    vs. `DELETE FROM` :
    `DROP TABLE` 은 DDL로 테이블의 존재를 삭제하고, `DELETE FROM` 은 DML로 테이블의 레코드를 삭제 (조건 삭제 가능)
    
- `ALTER TABLE`
    
    테이블 구조 변경
    
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