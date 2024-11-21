# [solvesql/SQLite] 가구 판매의 비중이 높았던 날 찾기

[Velog](https://velog.io/@semoon/solvesqlSQLite-가구-판매의-비중이-높았던-날-찾기)<br>
[문제](https://solvesql.com/problems/day-of-furniture/)

✔️ 난이도 4

## 문제 흐름
> 일별 주문 수가 10개 이상인 날 중에서,

➡️ 조건 1
`order_date`별 `DISTINCT order_id`의 수가 10 이상인 레코드를 필터링한다.
`GROUP BY` - `COUNT` 를 사용해서 구해야하는 값이므로 `HAVING`을 이용한다.

> order_date - 주문 날짜
furniture - 해당 일의 Furniture 카테고리 주문 수
furniture_pct - 해당 일의 전체 주문 대비 Furniture 카테고리 주문의 비율 (%)

➡️ 추출할 컬럼 확인
- `furniture`
`order_date`별 `category`가 'Furniture'인 `DISTINCT order_id`를 카운트해준다.
이를 위해 `CASE`문을 이용해 `category`가 'Furniture'인 경우 `order_id`값, 아닐 경우 `NULL`값을 반환하도록 하고, 결과값에 `DISTINCT`를 붙여 중복을 제거한다.
- `furniture_pct`
`furniture`의 계산값에 `100.0`을 곱하여 실수형으로 타입을 변환해준다.
다시 `DISTINCT order_id`의 수로 나눠 비율을 구한다.

> Furniture 카테고리의 주문 비율은 백분율로 계산하며, 반올림하여 소수점 둘째자리까지만 출력해주세요.

➡️ `furniture_pct` 계산시 `round`함수를 사용해 소수점 둘째자리까지 표현하도록 한다.

> ‘Furniture’ 카테고리 주문의 비율이 40% 이상 이었던 날만 출력하는 쿼리를 작성해주세요.

➡️ 조건 2
`furniture_pct`가 40 이상인 레코드를 필터링한다.

> Furniture 카테고리의 주문 비율이 높은 것부터 보여주도록 정렬하고, 비율이 같다면 날짜 순으로 정렬해주세요.

➡️ 3번 컬럼을 기준으로 `DESC`, 1번 컬럼을 기준으로 `ASC`로 정렬한다.



## 코드
```sql
SELECT  order_date,
        count(DISTINCT CASE WHEN category = 'Furniture' THEN order_id ELSE NULL END) furniture,
        round(count(DISTINCT CASE WHEN category = 'Furniture' THEN order_id ELSE NULL END) * 100.0 / count(distinct order_id), 2) furniture_pct
FROM records
GROUP BY order_date
HAVING count(DISTINCT order_id) >= 10
AND furniture_pct >= 40
ORDER BY 3 DESC, 1;
```
