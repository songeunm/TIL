# [solvesql/SQLite] 언더스코어(_)가 포함되지 않은 데이터 찾기

[Velog](https://velog.io/@semoon/solvesqlSQLite-언더스코어가-포함되지-않은-데이터-찾기)<br>
[문제](https://solvesql.com/problems/data-without-underscore/)

✔️ 난이도 2

## 문제 흐름
> page_location 컬럼의 값이 언더스코어('\_')를 포함하지 않는 경우만 출력하는 쿼리를 작성해주세요. 

➡️ 문자열 탐색<br>
`LIKE`를 통한 문자열 탐색에서 **`_`는 "한 문자"를 나타내는 와일드카드**이고,
**`%`는 "문자열"을 나타내는 와일드카드**이다.
와일드카드인 언더스코어 포함 여부를 탐색하기 위해서는 **이스케이프 처리**가 필요하다.
SQL에서 이스케이프 처리는 백슬래시`\`를 사용한다.
하지만 어째서인지 검색이 제대로 이루어지지 않았다.

서치해보고 알게 되었는데,
SQLite에서는 찾고자하는 패턴에 `\`를 통해 이스케이프 처리를 한 뒤
뒤에 `ESCAPE '\'`를 붙여줘야 제대로 작동한다고 한다.

> page_location 컬럼만 있어야 하고, 중복되는 값은 1번만 나와야 하며 오름차순으로 정렬되어 있어야 합니다.

➡️ 중복제거, 정렬<br>
출력하는 `page_location` 컬럼은 `DISTINCT`를 통해 중복을 제거하고,
이 컬럼값을 기준으로 오름차순으로 정렬한다.


## 코드
```sql
SELECT DISTINCT page_location
FROM ga
WHERE page_location NOT LIKE '%\_%' ESCAPE '\'
ORDER BY 1;
```
