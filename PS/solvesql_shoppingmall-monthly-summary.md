# [solvesql/SQLite] 온라인 쇼핑몰의 월 별 매출액 집계

[Velog](https://velog.io/@semoon/solvesqlSQLite-온라인-쇼핑몰의-월-별-매출액-집계)<br>
[문제](https://solvesql.com/problems/shoppingmall-monthly-summary/)

✔️ 난이도 3

## 문제 흐름
> 위 두 테이블의 데이터를 조합해 월 별로 취소 주문을 제외한 주문 금액의 합계, 취소 주문의 금액 합계, 그리고 총 합계를 계산하는 쿼리를 작성해주세요. order_id가 C로 시작하는 주문이 취소 주문입니다.

➡️ 집계 내용
문제에서 위 두 테이블이란 `orders`와 `order_items`를 말한다.

|<img src=https://velog.velcdn.com/images/semoon/post/9ba6f1ce-e394-47e0-9dbb-751303d9b0aa/image.png width=100%>|<img src=https://velog.velcdn.com/images/semoon/post/927d70e2-5345-4e6a-8c6f-4b20f42697af/image.png width=95%>|
|---|---|
두 테이블이 갖고있는 컬럼은 위와 같다.
취소 여부는 두 테이블이 모두 가지고있는 컬럼이며 join의 key가 되는 컬럼인 `order_id`,
그룹화의 기준이 되는 컬럼인 `order_date`,
주문 금액 합계를 계산할 때 필요한 `price`, `quantity`가 있다.
베이스 테이블은 `order_items`로 잡아 LEFT JOIN을 했다.

"월 별"으로 만들어주기 위해 `order_date`에서 `substr`을 통해 월까지만 잘라주고,
이를 기준으로 그룹화를 진행한다.
`CASE`문을 이용해 `order_id`가 "C"로 시작하지 않는다면 금액(가격과 수량의 곱),
"C"로 시작한다면 0을 반환하도록 작성하고, 이를 sum한다.
취소 주문 금액 합계는 이와 반대로 작성하면 된다.
총 합계는 `CASE`문 없이 작성하면 된다.

> order_month 컬럼의 값으로 오름차순 정렬되어 있어야 합니다.

➡️ 정렬
마지막으로 첫번째 컬럼을 기준으로 오름차순 정렬한다.


## 코드
```sql
SELECT
  substr(order_date, 1, 7) order_month,
  sum(CASE WHEN i.order_id NOT LIKE 'C%' THEN price * quantity ELSE 0 END) ordered_amount,
  sum(CASE WHEN i.order_id LIKE 'C%' THEN price * quantity ELSE 0 END) canceled_amount,
  sum(price * quantity) total_amount
FROM order_items i
LEFT JOIN orders o ON i.order_id = o.order_id
GROUP BY 1
ORDER BY 1;
```
