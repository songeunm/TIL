# [solvesql/SQLite] 지역별 주문의 특징 (+ PIVOT)

[Velog](https://velog.io/@semoon/solvesqlSQLite-지역별-주문의-특징-PIVOT)<br>
### 📍PIVOT 함수?
**행의 데이터를 열로 변환**하여 요약 정보를 표시하는 방법이다.
- 주로 데이터를 요약하거나 집계하기 위해 사용
- 특정 열의 고유 값들을 **새로운 열로 변환**하고, 다른 열의 값을 이 열에 대응하여 집계
```sql
SELECT [fixed_column], [new_column_based_row_values]
FROM (
    SELECT [original_columns]
    FROM [original_table]
) AS SourceTable
PIVOT (
    [Aggregation Function](column_to_aggregate)
    FOR [column_to_transform] IN ([new_column_1], [new_column_2], [new_column_3])
) AS PivotTable;
```
- `fixed_column`: 피봇테이블으로 **변환 후에도 유지**되는 열 (그룹화 기준 1)
- `new_column_based_row_values`: 레코드 값을 통해 새로운 컬럼을 형성할 컬럼
- `original_columns`/`original_table`: 원본 컬럼과 테이블
- `Aggregation Function`/`column_to_aggregate`: 집계 함수와 집계할 컬럼
- `column_to_transform`: **피봇테이블의 새로운 열**로 만들 컬럼 (그룹화의 기준 2)
- `new_column`: `column_to_transform` 컬럼의 값들
- `AS SourceTable`/`AS PivotTable`: 일부 RDBMS(SQL Server, Oracle 등)에서는 서브쿼리와 `PIVOT`에 엘리어스가 필수적이므로 붙이는 편이 좋다.
---
[문제](https://solvesql.com/problems/characteristics-of-orders/)

✔️ 난이도 3

## 문제 흐름
> region, category 별 주문량을 계산해 출력하는 쿼리를 작성해주세요.

➡️ 계산 내용
계산하고자 하는 내용은 간단하다.
`region`, `category` 별 `order_id`를 카운트해주면 되겠다.
`order_id`를 카운트하는 것이니 `DISTINCT`를 통해서 중복을 제거해야한다.

> 결과 데이터는 아래와 같은 테이블 형태로 출력되어야 하고, Region 컬럼 기준 오름차순으로 정렬되어 있어야 합니다.
<img src=https://velog.velcdn.com/images/semoon/post/6dea902b-85f8-4c34-afb1-169de050afa9/image.png width=70%>

➡️ 결과 데이터 형식
여기가 살짝 고민을 하게되는 부분이다.
그룹화를 두 컬럼을 기준으로 진행해야하는데, 이중 하나를 `CASE`문을 통해 분리함으로 해결해준다.
`CASE`문을 통해 `category`가 각각 `Furniture`, `Office Supplies`, `Technology`인 경우 `order_id`를 반환하도록 작성한다.
그리고 이를 `count` 함수를 통해 집계하는데, `CASE`문 앞에 `DISTINCT`를 	붙여 중복되는 `order_id`를 제거하고 집계하도록 한다.
이렇게 계산한 컬럼에 각 필터링한 카테고리명을 붙여준다.

이와 같은 테이블 형식을 만드는 방법으로 `PIVOT` 함수를 이용하는 방법도 있지만,
SQLite에서는 이를 지원하지 않기 때문에 사용할 수 없었다.
만약 `PIVOT` 함수를 이용한다면 다음과 같은 코드가 될 수 있겠지만, 결과를 확인해보지 못했기 때문에 정확하지 않다.
```sql
SELECT region "Region", "Furniture", "Office Supplies", "Technology"
FROM (
  SELECT region, category, order_id
  FROM records
) AS base_table
PIVOT (
  count(order_id)
  FOR category IN ("Furniture", "Office Supplies", "Technology")
) AS pivot_table
ORDER BY 1;
```
또한 위의 코드에서 컬럼명을 표시할 때 더블쿼트`" "`를 사용했는데, 이는 Oracle에서는 정상작동하지만 SQL Server의 경우 대괄호`[ ]`를 사용해야한다고 한다.
더블쿼트를 사용하는 것이 SQL 표준이나, 종종 백틱`` ` ` ``을 사용해야 하는 경우도 있다고 하니 어떤 RDBMS를 사용하는지 확인하고 이에 맞는 문법을 체크하는 것이 좋겠다.


## 코드
```sql
SELECT
  region "Region",
  count(
  	DISTINCT CASE WHEN category = 'Furniture' THEN order_id ELSE NULL END
  ) "Furniture",
  count(
  	DISTINCT CASE WHEN category = 'Office Supplies' THEN order_id ELSE NULL END
  ) "Office Supplies",
  count(
  	DISTINCT CASE WHEN category = 'Technology' THEN order_id ELSE NULL END
  ) "Technology"
FROM records
GROUP BY region
ORDER BY region;
```

## 마무리
정말 뜬금없는 얘기지만 여지껏 `` ` ``를 "백쿼트"라고 읽고 말해왔는데, 이는 구어체.. 별명같은 비공식 명칭이고 "백틱"이 맞다고 한다.
