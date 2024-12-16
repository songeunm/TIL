# [solvesql/SQLite] 게임 개발사의 주력 플랫폼 찾기 (+ 집계 함수와 윈도우 함수의 실행 순서)

[Velog](https://velog.io/@semoon/solvesqlSQLite-게임-개발사의-주력-플랫폼-찾기-집계-함수와-WINDOW-함수의-실행-순서)

### 📍 집계 함수와 윈도우 함수의 실행 순서

집계 함수를 사용할 때의 `GROUP BY`와 윈도우 함수의 `PARTITION BY`는 비슷하게 그룹화를 하는 역할을 한다.
집계 함수와 윈도우 함수가 동시에 사용되는 경우 이의 실행 순서는 어떻게 될까?

<img src=https://velog.velcdn.com/images/semoon/post/671baedd-056d-46bc-9551-a8d8dccc8379/image.png width=70%>

**윈도우 함수는 SQL에서 실행 순서 중 SELECT 단계에서 수행된다.**
즉 GROUP BY보다 뒤에 위치하게 된다.
윈도우 함수는 `GROUP BY`에 의해 그룹화가 이루어진 뒤,
그 결과에 대하여 다시 `PARTITION BY`에 따라 그룹화를 진행하여 값을 계산한다.
아래의 예시를 통해 살펴보자!

#### 💡 윈도우 함수 ( 집계 함수 ) 인 경우
GROUP BY에 따라 그룹화 진행 후 집계 함수 계산 수행
-> 집계 함수 결과에 대해서 다시 PARTITION BY에 따라 그룹화 진행 후 윈도우 함수 계산 수행

#### 💡 집계 함수 ( 윈도우 함수 ) 인 경우
GROUP BY에 의한 그룹화가 진행되지 않은 상태에서 PARTITION BY에 따라 그룹화 진행 후 윈도우 함수 계산 수행
-> 윈도우 함수 결과에 대해서 GROUP BY에 따라 그룹화 진행 후 집계함수 계산 수행

---

[문제](https://solvesql.com/problems/main-platform-of-game-developers/)

✔️ 난이도 4

## 문제 흐름
> **개발사마다 주력 플랫폼은 보통 판매량이 가장 많은 플랫폼으로 생각할 수 있습니다. **
games 테이블, companies 테이블, platforms 테이블을 조회해 각 게임 개발사의 주력 플랫폼과 해당 플랫폼의 판매량 합계를 집계하는 쿼리를 작성해주세요.
쿼리 결과에는 아래 컬럼이 포함되어야 합니다. **만약, 판매량 합계가 동일한 플랫폼이 여러 개라면 모두 출력해주세요.**
- developer: 게임 개발사 이름
- platform: 주력 플랫폼 이름
- sales: 게임 개발사의 주력 플랫폼 판매량 합계

`games` 테이블에는 `developer_id`, `platform_id`, 그리고 각 지역별 판매량 정보가 있다.
주력 플랫폼을 구하기 위해서는 개발사, 플랫폼 별로 각 지역 판매량을 모두 더한 총 판매량을 구해야한다.
<img src=https://velog.velcdn.com/images/semoon/post/f94d44c7-d9b3-4ff5-b2ed-9b77fa1f85a9/image.png width=30%>
이는 간단하게 `developer_id`와 `platform_id`로 그룹화하여 sum을 계산하는 것으로 구할 수 있다.

총 판매량을 구했다면 가장 판매량이 많은 주력 플랫폼을 찾아야한다.
여기서 조건에 *만약, 판매량 합계가 동일한 플랫폼이 여러 개라면 모두 출력해주세요.* 라고 했으므로 같은 수치에 대해 동일한 순위를 부여하는 **rank**를 이용했다.
위에서 계산한 총 판매량을 기준으로 rank함수를 통해 순위를 부여하고, `sales_rank`라는 엘리어스를 붙였다.

이렇게 rank를 계산해 하나의 컬럼으로 넣었는데, 결과에서는 rank를 출력하지 않아야하므로 여기까지의 결과를 `calc_rank`라는 CTE로 작성했다.
메인 쿼리에서 `sales_rank`가 1인 레코드만 필터링한다.
`calc_rank`에서는 개발사와 플랫폼 정보가 id로 되어있으므로 `companies`, `platform` 테이블을 LEFT JOIN하여 각각의 이름을 가져와 출력했다.




## 코드
```sql
WITH
  calc_rank AS (
    SELECT
      developer_id,
      platform_id,
      sum(sales_na + sales_eu + sales_jp + sales_other) sales,
      rank() OVER (
        PARTITION BY developer_id
        ORDER BY sum(sales_na + sales_eu + sales_jp + sales_other) DESC
      ) sales_rank
    FROM games
    WHERE developer_id IS NOT NULL
    GROUP BY 1, 2
  )

SELECT
  c.name developer,
  p. name platform,
  sales
FROM calc_rank r
LEFT JOIN companies c ON r.developer_id = c.company_id
LEFT JOIN platforms p ON r.platform_id = p.platform_id
WHERE sales_rank == 1;
```
