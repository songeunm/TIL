# [solvesql/SQLite] 폐쇄할 따릉이 정류소 찾기 1

[Velog](https://velog.io/@semoon/solvesqlSQLite-폐쇄할-따릉이-정류소-찾기-1)<br>
[문제](https://solvesql.com/problems/find-unnecessary-station-1/)

✔️ 난이도 5

## 문제 흐름
일단 문제 풀이 자체는 그렇게 어렵지 않습니다만 ...
계산이 복잡해서 머리가 살짝 아픈 느낌이다.

> 정류소 정보가 저장된 station 테이블을 사용해 반경 300m 이내에 나중에 생기거나 업그레이드 된 다른 정류소가 5개 이상 있는 따릉이 대여소의 아이디(station_id)와 이름(name)을 출력하는 쿼리를 작성해주세요.

➡️ 조건 및 계산 내용
거리 계산과 업데이트 시기 비교를 위해서는 **`station`테이블을 `SELF JOIN`** 해줄 필요가 있다.
`SELF JOIN`을 할 때 자기자신과 비교할 필요는 없으므로 조건에 `station_id`가 같지 않음을 넣어주고,
*"나중에 생기거나 업그레이드 된"* 조건을 `updated_at` 비교를 통해 함께 명시하여 처리해준다.
또 이렇게 비교를 진행한 레코드만 남겼을 때, 베이스 테이블의 `station_id`를 기준으로 `GROUP BY`하고, 레코드를 카운트해서 5개가 넘는 레코드만 남기도록 `HAVING`절을 사용해 필터링해준다.

> 서로 다른 두 지점의 위도와 경도 정보를 통해 두 지점의 거리를 구할 때는 Haversine formula를 사용합니다. 장소 1의 위치를 나타내는 라디안(radian) 단위의 위도와 경도를 각각 ρ1, λ1, 장소 2의 위치를 나타내는 라디안(radian) 단위의 위도와 경도를 각각 ρ2, λ2, 그리고 지구의 반지름을 r=6356km라고 할 때, 두 장소의 거리(d)는 아래와 같습니다.
![](https://velog.velcdn.com/images/semoon/post/5be217a1-26fb-4aa8-9c18-ecc172d66a73/image.png)

➡️ 계산 내용
이 식을 이용하여 거리 계산을 해야한다.
필요한 함수는 다음과 같다.
```sql
asin(라디안 단위 각도)	-- arcsin
sin(라디안 단위 각도)	-- sin
cos(라디안 단위 각도)	-- cos
power(밑, 지수)    -- 제곱
sqrt(피제곱수)      -- 루트
```
식이 길기는 하지만 계산은 식대로 잘 적어주기만 하면 된다.
주의할 점은 2가지 정도 있다.

1. r은 "km"단위이고, 조건에 들어갈 300은 "m"단위이다.
단위를 맞춰주기 위해 조건을 0.3으로 작성했다.
2. 삼각함수의 인자는 라디안 단위로 들어가야하고, 주어진 위도 경도는 도(dgree)단위이다.
단위를 맞춰주기 위해 계산할 때 모든 위도·경도에 `*3.14/180`을 붙여주었다.

이렇게 계산한 거리를 `WHERE`절에 조건으로 넣어 필터링 해준다.

## 코드
```sql
-- solution 1:  self join
WITH
  calc AS (
    SELECT
      *
    FROM
      station s1
      JOIN station s2 ON s1.station_id <> s2.station_id
      AND s1.updated_at < s2.updated_at
    WHERE
      2 * 6356 * asin(
        sqrt(
          power(
            sin(
              ((s1.lat * 3.14 / 180) - (s2.lat * 3.14 / 180)) / 2
            ),
            2
          ) + cos((s1.lat * 3.14 / 180)) * cos((s2.lat * 3.14 / 180)) * power(
            sin(
              ((s1.lng * 3.14 / 180) - (s2.lng * 3.14 / 180)) / 2
            ),
            2
          )
        )
      ) <= 0.3
    GROUP BY
      s1.station_id
    HAVING
      count(*) >= 5
  )
SELECT
  station_id,
  name
FROM
  calc;
  
  
-- solution 2: full outer join
SELECT
  s1.station_id,
  s1.name
FROM station s1
FULL OUTER JOIN station s2
ON s1.station_id <> s2.station_id
WHERE
  2 * 6356 * asin(sqrt(power(sin(((s1.lat*3.14/180)-(s2.lat*3.14/180))/2), 2) + cos(s1.lat*3.14/180)*cos(s2.lat*3.14/180)*power(sin(((s1.lng*3.14/180)-(s2.lng*3.14/180))/2), 2))) <= 0.3
  AND s1.updated_at < s2.updated_at
GROUP BY s1.station_id
HAVING count(*) >= 5;
```

## 마무리
사실 solution 2가 내가 처음부터 풀었던 방식이고
계산이 너무 꼬이면서 라디안 단위 변환을 놓쳐 헤메다가 gpt의 도움을 받았었다.
근데 확실히 sql 포맷팅을 한 쿼리랑 안한 쿼리를 나란히 놓고 보니
익숙하지 않은 나에게는 아랫쪽이 더 깔끔한 것 같기도 하고 그렇다...
괜히 괄호도 많아지고 하니 더 복잡하게만 사용된 느낌인데,
포맷팅쪽에 익숙해지는게 좋겠지?
