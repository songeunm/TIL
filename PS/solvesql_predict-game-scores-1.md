# [solvesql/SQLite] 게임 평점 예측하기 1

[Velog](https://velog.io/@semoon/solvesqlSQLite-게임-평점-예측하기-1)<br>
[문제](https://solvesql.com/problems/predict-game-scores-1/)

✔️ 난이도 3

## 문제 흐름
> 2015년 이후에 발매한 게임 중 누락된 평점 정보가 있는 게임에 대해서

> 쿼리 결과에는 누락된 평점 정보가 있는 게임만 포함되어야 하며,

➡️ 조건<br>
`year`가 2015보다 크고, `critic_score`나 `user_score`가 `NULL`값인 레코드에 대해 작업을 수행한다.
여기서 `critic_score`가 `NULL`값이거나 `user_score`가 `NULL`값인 조건을 부여할 때 이는 괄호로 묶어 먼저 수행되도록 작업해줘야 한다.
아니면 `AND`가 먼저 수행되어 필터링이 제대로 이루어지지 않을 수 있다.

> 같은 장르를 가진 전체 게임의 평균 평점과 평균 평론가/사용자 수를 사용해 누락된 정보를 채우는 쿼리를 작성해주세요. 

➡️ 작업<br>
NULL값을 처리하는 작업을 해야한다.
서브쿼리를 이용하여 작성하였는데, 각 장르별로 레코드의 값을 정하기 위해
베이스 테이블에 `main`이라는 엘리어스를 준 뒤,
서브쿼리에서 `WHERE`절에 `main.genre_id`와 `genre_id`가 일치하도록 조건을 줬다.

일부 데이터를 확인해보면 알 수 있듯이
`critic_score`와 `user_score`가 동시에 누락된 레코드도 있지만 둘 중 하나만 누락된 레코드도 존재한다.
따라서 둘중 하나의 값이 존재한다면 그 값은 평균값이 아니라 기존값을 사용해야하므로
`CASE`문을 이용해 `NULL`값인 경우에만 계산값을 가져오도록 했다.

> 또한, 평론가와 사용자 평점 평균은 소수점 아래 넷째 자리에서 반올림 해 셋째 자리까지 출력되어야 하고, 사용자 수는 올림하여 자연수로 출력되어야 합니다.

➡️ 데이터 포맷팅

평점 컬럼은 `round`함수를 활용해서 반올림하여 소수점 셋째자리까지 나타내도록 하고,
사용자 수 컬럼은 "올림"해야하기 떄문에 `ceil`함수를 사용하여 자연수로 나타내도록 했다.

## 코드
```sql
-- 2015년 이후에 발매한 게임 중
-- 누락된 평점 정보가 있는 게임에 대해서
-- 같은 장르를 가진 전체 게임의 평균 평점과 평균 평론가/사용자 수를 사용해 누락된 정보를 채우는 쿼리
SELECT
  game_id,
  name,
  CASE WHEN critic_score IS NULL
    THEN (SELECT round(avg(critic_score), 3) FROM games WHERE genre_id = main.genre_id)
    ELSE critic_score
  END critic_score,
  CASE WHEN critic_count IS NULL
    THEN (SELECT ceil(avg(critic_count)) FROM games WHERE genre_id = main.genre_id)
    ELSE critic_count
  END critic_count,
  CASE WHEN user_score IS NULL
    THEN (SELECT round(avg(user_score), 3) FROM games WHERE genre_id = main.genre_id)
    ELSE user_score
  END user_score,
  CASE WHEN user_count IS NULL
    THEN (SELECT ceil(avg(user_count)) FROM games WHERE genre_id = main.genre_id)
    ELSE user_count
  END user_count
FROM games main
WHERE
  year >= 2015
  AND (critic_score IS NULL OR user_score IS NULL);
```
