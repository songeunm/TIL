# [solvesql/SQLite] 스테디셀러 작가 찾기

[Velog](https://velog.io/@semoon/solvesqlSQLite-스테디셀러-작가-찾기)

[문제](https://solvesql.com/problems/find-steadyseller-writers/)

✔️ 난이도 5

## 문제 흐름
> books 테이블의 데이터를 사용해 **5년 이상 연속으로** 베스트셀러 작품 목록에 이름을 올린 **소설 작가**와 연도 정보를 출력하는 쿼리를 작성해주세요.
쿼리 결과는 아래 결과를 포함해야 합니다.
- author - 작가 이름
- year - **연속 베스트셀러 기간 중 가장 최근 연도**
- depth - **연속 베스트셀러 년수**

첫번째로 동일 연도에 베스트셀러에 여러권 올린 작가가 있을 수 있으니 `author`, `year`를 뽑아 DISTINCT를 통해 중복을 제거하고, 소설 작가만 필터링한다.
~~여기서 소설 작가임을 몰라서 진짜 오래 고민했다..... 어쩐지 레코드가 너무 많더라~~
이를 `filtering`이라는 CTE로 만들었다.

연속성은 `lag`함수를 통해 이전 연도 값을 가져와서 그 차가 1인지 아닌지를 통해 확인할 수 있다. 차를 구하기 위해서는 `lag`함수를 적용해서 만든 새로운 컬럼 `prev_year`를 가진테이블이 필요해서 이를 `clac_prev_year`라는 CTE로 만들었다.
`lag` 함수를 적용할 때에는 `author`를 기준으로 그룹화하고, `year`를 기준으로 정렬해야한다.

CASE문을 통해 `year - prev_year`가 1인 경우 0을 반환하고,
가장 첫번째 연도여서 `prev_year`가 `NULL`인 경우와
연속 연도가 아니어서 `year - prev_year`가 1 이상인 경우는 모두 1을 반환하여
새로운 그룹의 시작점을 1로 표시했다.
그리고 이를 `sum over`로 누적합을 구해 새로운 컬럼을 생성했다.
이는 각 연속 연도별 그룹 넘버링을 나타낸다.
컬럼명은 `group_num`으로 지정했고, 이를 다시 `clac_group_num`이라는 CTE로 만들었다.

이 `calc_group_num`에서 `author`와 `group_num`을 기준으로 그룹화하여
`author`, 그룹별 가장 최근 연도 `max(year)`, 그룹별 연속 베스트셀러 년수 `count(*)`를 집계한다.
그리고 연속 베스트셀러 년수가 5 이상이 되도록 HAVING을 통해 필터링한다.

## 코드
```sql
WITH
  filtering AS (
    SELECT DISTINCT
      author,
      year
    FROM books
    WHERE genre = 'Fiction'
  ), calc_prev_year AS (
    SELECT
      author,
      year,
      lag(year, 1) OVER (PARTITION BY author ORDER BY year) prev_year
    FROM filtering
  ), calc_group_num AS (
    SELECT
      author,
      year,
      sum(
        CASE
        WHEN year - prev_year = 1 THEN 0 ELSE 1
        END
      ) OVER (PARTITION BY author ORDER BY year) group_num
    FROM calc_prev_year
  )

SELECT
  author,
  max(year) year,
  count(*) depth
FROM calc_group_num
GROUP BY author, group_num
HAVING count(*) >= 5
```
