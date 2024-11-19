# [solvesql/SQLite] 우리 플랫폼에 정착한 판매자 1

[Velog](https://velog.io/@semoon/solvesqlSQLite-우리-플랫폼에-정착한-판매자-1)<br>
[문제](https://solvesql.com/problems/settled-sellers-1/)

✔️ 난이도 1

## 문제 흐름
예전에 풀다가 틀린채로 잠시 접어뒀던 문제를 찾았다.
문제는 참 간단한데 왜 틀렸을까 봤는데 데이터에 대한 이해가 부족했던 것 같다.

데이터를 잘 살펴보면 컬럼에 대한 설명이 다음과 같이 되어있다.

`string` `order_id` 주문 ID
`integer` `order_item_id` 주문 내 상품 순서
`string` `product_id` 상품 ID
`string` `seller_id` 판매자 ID
`datetime` `shipping_limit_date` 발송 제한 일자
`number` `price` 가격
`number` `freight_value` 운송료

`order_item_id` 컬럼이 있는 것으로 보아 하나의 주문 건에 대하여 각 상품이 레코드로 흩어져있음을 알 수 있다.
그러면 주문 ID가 여러번 중복되어 있을거고, 이를 제거해줘야한다.
따라서 `order_id`를 카운트할 때 앞에 `DISTINCT`를 붙여 중복을 제거해주면 된다.
`HAVING`절에서 주문건 100개 이상의 조건을 작성할 때도 마찬가지다.

## 코드
```sql
SELECT seller_id, COUNT(DISTINCT order_id) AS orders
FROM olist_order_items_dataset
GROUP BY seller_id
HAVING COUNT(DISTINCT order_id) >= 100;
```

## 마무리
데이터가 기존에 sql 문제를 풀던 사이트인 프로그래머스보다 좀더 디테일한 면이 있다.
항상 데이터는 대충 보고 필요한 컬럼만 쏙쏙 뽑아서 대충 이해하는 식으로 했는데 안좋은 습관 들였구나, 체감했다.
