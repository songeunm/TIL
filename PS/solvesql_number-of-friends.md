# [solveslq/SQLite] 친구 수 집계하기

[Velog](https://velog.io/@semoon/solveslqSQLite-친구-수-집계하기)

[문제](https://solvesql.com/problems/number-of-friends/)

✔️ 난이도 3

## 문제 흐름
> edges 테이블에는 사용자의 친구 관계 정보가 들어있는데 각 행의 user_a_id 컬럼 사용자와 user_b_id 컬럼 사용자가 서로 친구 관계라는 의미 입니다.
**데이터베이스에 포함된 모든 사용자**에 대해 각 **사용자의 친구 수를 집계해 출력하는 쿼리**를 작성해주세요.
쿼리 결과에는 아래 컬럼이 포함되어 있어야 하고, **친구 수가 많은 사용자부터 출력**되어야 합니다.
만약 친구 수가 같은 사용자가 여럿이라면 그 사이에서는 **사용자 ID가 작은 사용자가 먼저 출력**되어야 합니다.
- user_id: 사용자 ID
- num_friends: 친구 수

보기와 다르게 시간이 오래 걸린 문제다.
어느부분에서 친구 수가 더 많게 카운트된건지 아직도 잘 모른다.
그래서 아예 새롭게, 좀 더 명확한 방식으로 진행했다.

일단 첫번째 주의할 점은 *"데이터베이스에 포함된 모든 사용자에 대해"*라는 부분이다.
데이터베이스는 `edges`테이블만을 말하는 것이 아니다.
모든 유저에 대한 정보가 담겨있는 테이블 `users`, 특성에 대한 정보가 담겨있는 `features` 모두 다 포함된 의미이다.
**즉 `users`테이블을 기준으로 해야한다.**
그래야 친구가 없어서 `edges`에 따로 기록되지 않은 사용자까지 체크할 수 있다.

두번째 주의할 점은 이 `edges`테이블에서 `user_a_id`와 `user_b_id`는 **양방향**관계라는 것이다.
따라서 `user_a_id`에서만 체크하면 안되고, `user_b_id`에서도 체크해줘야한다.
다른 방법들도 있겠지만 좀더 명확하게 진행하기 위해 `user_a_id`와 `user_b_id`을 기준으로 각각 join을 진행하고 그 수를 집계한 뒤, 이를 개별적인 cte로 생성했다.
이를 메인 쿼리에서 불러와 그 집계값의 합을 통해 `num_friends`를 계산했다.

정렬은 친구 수 `num_friends`를 기준으로 내림차순 후, 사용자 아이디 `user_id`를 기준으로 오름차순 정렬한다.

## 코드
```sql
WITH
  calc_1 AS (
    SELECT
      user_id,
      count(user_b_id) cnt1
    FROM users u
    LEFT JOIN edges e1 ON u.user_id = e1.user_a_id
    GROUP BY 1
  ),
  calc_2 AS (
    SELECT
      user_id,
      count(user_a_id) cnt2
    FROM users u
    LEFT JOIN edges e2 ON u.user_id = e2.user_b_id
    GROUP BY 1
  )

SELECT
  user_id,
  cnt1 + cnt2 num_friends
FROM calc_1
JOIN calc_2 USING (user_id)
ORDER BY 2 DESC, 1;
```
