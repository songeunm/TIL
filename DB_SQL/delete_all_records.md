# DELETE FROM / TRUNCATE / DROP TABLE

[Velog](https://velog.io/@semoon/DELETE-FROM-TRUNCATE-DROP-TABLE)<br>
셋 모두 데이터를 삭제한다는 점에서 공통점이 있다.
다만 그 세부적인 작동 방법과 내용에 차이가 있다.

## `DELETE FROM`
```sql
DELETE FROM table_name
WHERE column_1 = condition;
```
- 테이블에서 레코드를 삭제
- `WHERE`절을 통해 일부 레코드만 삭제 가능
- 속도가 느림
- DML
## `TRUNCATE`
```sql
TRUNCATE TABLE table_name;
```
- 테이블에서 레코드를 **전부** 삭제
- `WHERE` 사용 불가능
- 트랜잭션 지원하지 않음
- 전체 삭제 명령이기 때문에 속도 빠름
- DDL
## `DROP TABLE`
```sql
DROP TABLE table_name;
```
- 테이블 자체를 삭제 → 테이블 스키마 자체를 삭제함
- 속도가 가장 빠름
- DDL

## 테이블로 비교

||DELETE FROM| TRUNCATE | DROP TABLE |
|---|---|---|---|
|테이블 구조 삭제| X | X | O |
|레코드 일부 삭제| O | X | X |
|트랜잭션 지원| O | 대부분 X |대부분  X |
|속도| 느림 | 빠름 | 가장 빠름 |
|ROLLBACK | O | 대부분 X | 대부분 X|
