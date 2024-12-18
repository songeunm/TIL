# [solvesql/SQLite] 폐쇄할 따릉이 정류소 찾기 2

[Velog](https://velog.io/@semoon/solvesqlSQLite-폐쇄할-따릉이-정류소-찾기-2)

[문제](https://solvesql.com/problems/find-unnecessary-station-2/)

✔️ 난이도 3

## 문제 흐름
> **2019년 10월 한 달 동안 정류소에서 발생한 대여/반납 건수가 2018년 10월 같은 정류소에서 발생한 대여/반납 건수의 50% 이하인 정류소를 출력**하는 쿼리를 작성해주세요. 
2018년 10월 또는 2019년 10월 한 달간 대여/반납 **건수가 0건인 정류소**는 이미 폐쇄된 정류소이거나 새로이 생긴 정류소 일 수 있으므로 **쿼리 결과에 포함되지 않도록** 해주세요.
2018년 10월 대비 2019년 10월 정류소 대여/반납 건수 비율 값은 소수점 아래 셋째 자리에서 **반올림해 둘째 자리까지 출력**되어야 합니다.
쿼리 결과에는 아래 컬럼이 포함되어 있어야 합니다.
- station_id: 정류소 ID
- name: 정류소 이름
- local: 정류소 소속 지자체
- usage_pct: 2018년 10월 대비 2019년 10월 정류소 대여/반납 건수 비율 (0이상 100이하의 값)

`rental_history` 테이블을 확인해보면 한 레코드 안에 `rent_at`과 `rent_station_id`, `return_at`과 `return_station_id`가 모두 들어있음을 알 수 있다.
대여 및 반납 건수를 확인해야 하기 때문에 대여와 반납을 한 컬럼으로 몰면 집계가 편할거라고 행각했다.

그래서 `rentaㅣ_history`로부터 2018년 10월과 2019년 10월의 대여 시각과 대여 정류소 id를 뽑고, 다시 같은 기간의 반납 시각과 반납 정류소 id를 뽑아 이 두 결과를 `UNION ALL`을 통해 병합했다.
여기서 `UNION`을 사용하면 중복을 제거하기 때문에 레코드가 유실될 수 있으니 반드시 `UNION ALL`을 사용해줘야한다.

이 병합 결과 테이블로부터 `CASE`문을 2018년도인 경우 1, 아닌 경우 NULL을 리턴하도록 하고, 2019년도도 마찬가지로 새로운 컬럼으로 만든다.
이 각각에 sum을 적용해서 목표기간별 건수를 집계한다.
여기서 `0`이 아닌 `NULL`로 설정해줘야 나중에 비율을 구할 때 2018년도나 2019년도 건수가 0인 경우 계산을 NULL로 받을 수 있고, WHERE문을 통해 NULL값 제외하여 한번에 필터링할 수 있다.

위의 내용을 CTE `calc_usage`로 생성한 뒤, `usage_pct`를 계산하고 `station` 테이블을 LEFT JOIN하여 `name`, `local`정보를 가져온다.
그리고 앞서 말한 대로 NULL값을 필터링하고, `usage_pct`가 50 이하인 값을 필터링한다.

## 코드
```sql
WITH
  calc_usage AS (
    SELECT
      station_id,
      sum(CASE WHEN substr(rental_time, 1, 4) = '2018' THEN 1 ELSE NULL END) usage_2018,
      sum(CASE WHEN substr(rental_time, 1, 4) = '2019' THEN 1 ELSE NULL END) usage_2019
    FROM (
      SELECT
        rent_at rental_time,
        rent_station_id station_id
      FROM rental_history
      WHERE
        substr(rent_at, 1, 7) in ('2018-10', '2019-10')
      UNION ALL
      SELECT
        return_at rental_time,
        return_station_id station_id
      FROM rental_history
      WHERE
        substr(rent_at, 1, 7) in ('2018-10', '2019-10')
    )
    GROUP BY 1
  )

SELECT
  station_id,
  name,
  local,
  round(100.0 * usage_2019 / usage_2018, 2) usage_pct
FROM calc_usage
LEFT JOIN station USING (station_id)
WHERE
  usage_2019 / usage_2018 IS NOT NULL
  AND round(100.0 * usage_2019 / usage_2018, 2) <= 50
```
