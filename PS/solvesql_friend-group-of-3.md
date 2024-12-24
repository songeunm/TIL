# [solvesql/SQLite] 세 명이 서로 친구인 관계 찾기

[Velog](https://velog.io/@semoon/solvesqlSQLite-세-명이-서로-친구인-관계-찾기)

[문제](https://solvesql.com/problems/friend-group-of-3/)

✔️ 난이도 4

## 문제 흐름
> 소셜 네트워크 분석에서는 세 명의 사용자가 서로 친구 관계인 경우를 중요하게 생각합니다.
일반적인 사용자는 세 명의 사용자가 서로 친구인 경우가 다수 있지만, 스팸 사용자 또는 친구 관계를 무작위로 맺는 사용자의 경우 전체 친구 수에 비해 세 명의 사용자가 친구인 경우가 많지 않아 이상 사용자 탐지에 유용하게 쓸 수 있기 때문입니다.
주어진 데이터를 활용해 **ID가 3820인 사용자를 포함**해 **세 명의 사용자가 친구 관계인 경우를 모두 출력**하는 쿼리를 작성해주세요. 쿼리 결과에는 아래 컬럼이 포함되어 있어야 합니다.
중복된 세 친구 관계를 제외하기 위해 **user_a_id < user_b_id < user_c_id**를 만족하는 경우만 출력되어야 합니다.
- user_a_id: 친구 관계인 사용자 ID (A)
- user_b_id: 친구 관계인 사용자 ID (B)
- user_c_id: 친구 관계인 사용자 ID (C)

어떤 흐름으로 진행할지 고민을 좀 했다.
일단 쿼리문을 날려보면 확인해보면 알 수 있지만, 기존 `edges` 테이블에서 모든 레코드는`user_a_id` < `user_b_id`를 만족한다.
따라서 `user_b_id`가 3820인 레코드를 추출하면 3820보다 작은 id를 가지는 친구들의 id를 추출할 수 있고,
반대로 `user_a_id`가 3820인 레코드를 추출하면 3820보다 큰 id를 가지는 친구들의 id를 추출할 수 있다.
이 점을 이용해서 차례로 계산해줬다.
일단 위 두 경우의 친구 id를 추출하여 각각 `friends_of_3820_1`(3820보다 작은 id 친구), `friends_of_3820_2`(3820보다 큰 id 친구)라는 이름의 cte로 생성해줬다.

그리고 출력하는 `user_a_id`와 `user_b_id`가 모두 3820보다 작은 경우,
`user_a_id`는 3820보다 작고 `user_b_id`는 3820보다 큰 경우,
모두 3820보다 큰 경우에 대해 각각 쿼리문을 작성한 뒤
이 결과를 `UNION ALL`을 통해 통합하여 출력했다.

## 코드
```sql
WITH
  friends_of_3820_1 AS (
    SELECT
      user_a_id friend_id
    FROM edges
    WHERE user_b_id = 3820
  ),
  friends_of_3820_2 AS (
    SELECT
      user_b_id friend_id
    FROM edges
    WHERE user_a_id = 3820
  )

SELECT
  user_a_id,
  user_b_id,
  3820 user_c_id
FROM edges
WHERE
  user_a_id IN (SELECT * FROM friends_of_3820_1)
  AND user_b_id IN (SELECT * FROM friends_of_3820_1)

UNION ALL

SELECT
  user_a_id,
  3820 user_b_id,
  user_b_id user_c_id
FROM edges
WHERE
  user_a_id IN (SELECT * FROM friends_of_3820_1)
  AND user_b_id IN (SELECT * FROM friends_of_3820_2)

UNION ALL

SELECT
  3820 user_a_id,
  user_a_id user_b_id,
  user_b_id user_c_id
FROM edges
WHERE
  user_a_id IN (SELECT * FROM friends_of_3820_2)
  AND user_b_id IN (SELECT * FROM friends_of_3820_2);
```
