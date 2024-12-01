# [solvesql/SQLite] 배송 예정일 예측 성공과 실패

[Velog](https://velog.io/@semoon/solvesqlSQLite-배송-예정일-예측-성공과-실패)<br>
[문제](https://solvesql.com/problems/estimated-delivery-date/)

✔️ 난이도 3

## 문제 흐름
> 2017년 1월 한 달 동안 발생한 주문의 배송 예측이 정확했는지 분석을 하려고 합니다.

> 배송 완료 또는 배송 예정 시각 데이터가 없는 경우는 계산에서 제외합니다.

➡️ 조건

1. 구매 시각 `order_purchase_timestamp`가 `2017-01-01`부터 `2017-01-31` 사이에 존재하고,
2. 배송 완료 시각 `order_delivered_customer_date`와 배송 예정 시각 
`order_estimated_delivery_date`가 `NULL`이 아닌 레코드를 필터링해야한다.

여기서 구매 시각을 필터링 할 때 단순히 날짜만 넣어주면 `'2017-01-01 00:00:00' ~ '2017-01-31 00:00:00'` 사이에 해당하게 되므로 31일 주문 레코드가 누락될 수 있다.
이를 방지하기 위해서는 `'2017-01-31 23:59:59'`과 같이 시간까지 작성하거나 구매 시각을 `date`형으로 변환하여 조건을 넣어주면 된다.

> 고객의 구매 일자별로 배송 예정 시각 안에 고객에게 도착한 주문과, 배송 예정 시각이 지나서 고객에게 도착한 주문을 각각 집계하는 쿼리를 작성해주세요.

➡️ 집계 내용<br>
구매 시각 `order_purchase_timestamp`의 날짜 별로
배송 예정 시각 `order_estimated_delivery_date`이 배송 완료 시각 `order_delivered_customer_date`보다 빠르거나 같으면 `success`로 카운트,
늦으면 `fail`로 카운트한다.
`order_id`를 카운트하기 때문에 `CASE`문을 통해 `success`와 `fail`의 경우 각각 `order_id`를 반환하도록 작성하고 `DISTINCT`를 통해 중복을 제거한 뒤 `count`로 집계했다.

> 추가로 order_estimated_delivery_date 컬럼에는 주문 시점에 계산한 배송 예정 시각이 저장되어 있습니다. 예를 들어, 컬럼에 값이 ‘2017-02-24 00:00:00’로 들어있는 경우, 배송을 2017년 2월 24일 자정까지 완료하겠다는 의미를 담고 있습니다.

➡️ 조건 세부 사항<br>
이 부분을 통해서 `success`와 `fail`을 카운트할 조건문을 정확하게 작성할 수 있다.<br>
<img src=https://velog.velcdn.com/images/semoon/post/09c661ec-9f0a-4b42-8be1-a3a2144baceb/image.png width=30%><br>
데이터를 확인했을 때 배송 예정 시각의 값은 모두 시간을 포함하지 않음을 확인할 수 있다.
여기서 "자정"까지 배송함은 데이터에 적힌 대로 `2017-02-24` 날짜 시작인 `00:00:00`까지 배송함을 의미한다.
따라서 date형식으로 변환 후 **배송 완료 시각과 배송 예정 시각을 비교할 때 둘이 같다면** 해당 날짜의 자정을 지나서 왔다는 의미이므로 **`fail`**이다.

## 코드
```sql
SELECT
  date(order_purchase_timestamp) purchase_date,
  count(DISTINCT CASE
    WHEN date(order_delivered_customer_date) < date(order_estimated_delivery_date) THEN order_id ELSE NULL
  END) "success",
  count(DISTINCT CASE
    WHEN date(order_delivered_customer_date) >= date(order_estimated_delivery_date) THEN order_id ELSE NULL
  END) "fail"
FROM olist_orders_dataset
WHERE
  date(order_purchase_timestamp) BETWEEN '2017-01-01' AND '2017-01-31'
  AND order_delivered_customer_date IS NOT NULL
  AND order_estimated_delivery_date IS NOT NULL
GROUP BY 1
ORDER BY 1;
```
