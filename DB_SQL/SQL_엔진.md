[Velog](https://velog.io/@semoon/SQL-%EC%97%94%EC%A7%84-%EC%82%AC%EC%9A%A9)

## SQL 엔진 다운로드

```python
%load_ext sql
```

Python notebook을 sql editor처럼 사용하는 방법

python 코딩과 섞어서 진행 가능하다는 장점이 있다.

## DB에 연결

```python
%sql postgresql://id:pw@db_host:port_num/db_name
```

위는 Redshift postgresql를 예시로 작성

`%sql` 뒤에 Redshift 클러스터 정보를 위와 같이 입력함으로써 연결할 수 있다.

## SQL 쿼리 실행

```python
%%sql

SELECT * FROM raw_data.test_table;
```

`%%sql` 아래 한줄 공백을 둔 뒤 쿼리문을 작성하면 SQL 쿼리를 날릴 수 있다.