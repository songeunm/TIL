# FIRST_VALUE / LAST_VALUE / NTH_VALUE

[Velog](https://velog.io/@semoon/FIRSTVALUE-LASTVALUE-NTHVALUE)<br>
[MySQL 공식 문서](https://dev.mysql.com/doc/refman/8.4/en/window-function-descriptions.html)

각각 윈도우 프레임 안에서 (그룹별로) 첫번째/마지막/n번째 행의 값을 반환하는 윈도우 함수이다.

그룹별로 **정렬하여** 첫번째/마지막/n번째 값을 찾는 경우 사용할 수 있다.

- `FIRST_VALUE`

```sql
FIRST_VALUE(column_name) OVER ([PARTITION BY partition_column] [ORDER BY order_column])
```

- `LAST_VALUE`

```sql
LAST_VALUE(column_name) OVER ([PARTITION BY partition_column] [ORDER BY order_column])
```

- `NTH_VALUE`

```sql
NTH_VALUE(column_name) OVER ([PARTITION BY partition_clumn] [ORDER BY order_column])
```

<aside>
➕

윈도우 함수를 사용할 때 여러 윈도우 함수가 같은 `OVER` 절을 공유한다면, `OVER` 절은 다음과 같이 한번에 작성할 수 있다!

```sql
SELECT
	FIRST_VALUE(column_name) OVER w AS 'column_1',
	LAST_VALUE(column_name) OVER w AS 'column_2',
	NTH_VALUE(column_name) OVER w AS 'column_3'
FROM table
WINDOW w AS (PARTITION BY partition_column ORDER BY order_column);
```

</aside>
