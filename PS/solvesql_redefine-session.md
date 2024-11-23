# [solvesql/SQLite] 세션 재정의하기 (+ LAG / LEAD )

[Velog](https://velog.io/@semoon/solvesqlSQLite-세션-재정의하기-LAG-LEAD)<br>
[문제](https://solvesql.com/problems/redefine-session/)

✔️ 난이도 5

### 📍LAG / LEAD 란?
이전 또는 다음 행의 값을 참조하는 데 사용되는 윈도우 함수이다.
각각의 세부 사용법 및 특징과 차이점에 대해 알아보자.

#### 💡LAG
```sql
LAG(column_name, offset, default_value) 
OVER (PARTITION BY partition_column ORDER BY order_column)
```
- 현재 레코드의 **이전 레코드를 참조**
- `column_name`: 참조할 컬럼, 필수
- `offset`: 몇번째 이전 행을 가져올지 설정, 디폴트 1
- `default_value`: 이전 행이 없을 경우 사용할 기본값 설정, 디폴트 `NULL`

#### 💡 LEAD
```sql
LEAD(column_name, offset, default_value) 
OVER (PARTITION BY partition_column ORDER BY order_column)
```
- 현재 레코드의 **다음 레코드를 참조**
- `LAG`와 사용법은 동일하지만 *"다음"* 레코드를 가져온다는 차이점이 있음

## 문제 흐름
> 세션을 종료하는 기준을 사용자가 1시간 이상 행동하지 않을 때로 수정하여, 사용자 'S3WDQCqLpK’의 세션을 재정의하고

➡️ 변경 내용<br>
일단 세션을 새로 정의해주기 위해 각 레코드에 새로운 세션 넘버링을 붙이는 것을 목표로 하고, 순서대로 CTE를 생성하며 진행한다.

1. `sessions`
- 사용자 'S3WDQCqLpK'의 레코드만 변경해 확인하면 되므로
테이블 `ga`에서 `user_pseudo_id`가 'S3WDQCqLpK'인 레코드만 추출한다.
- 윈도우 함수 `LAG`를 통해 각 레코드의 이전 레코드 이벤트 시각`event_timestamp_kst`을 새로운 컬럼`bfr_timestamp`로 붙여준다.

2. `session_grouping`
- CTE `sessions`에서 `CASE` 문을 사용해
**현재 이벤트 시각**에서 **이전 이벤트 시각**을 뺀 값 `event_timestamp_kst - bfr_timestamp`가
1시간 이상인 경우 1, 아닌 경우 0이 되도록 컬럼 `session_group`을 생성해준다.
여기서 **1은 새로운 세션을 의미한다.**
따라서 `bfr_timestamp`가 비어있는 가장 첫번째 레코드는 1이 되도록 한다.
- 해당 사이트는 SQLite를 이용하고있고, **SQLite는 MySQL과 달리 `DATEDIFF`나 `TIMEDIFF`, `TIMESTAMPDIFF` 등의 날짜·시간을 계산하는 함수를 지원하지 않는다.**
대신 **`julianday`를 통해 일(day)단위로 변환할 수 있고,**
이를 산술적으로 계산해 차이를 구할 수 있다.

3. `session_numbering`
- CTE `session_grouping`에서 `SUM`을 이용해 `session_group`의 누적합을 계산하여 `session_group` 컬럼으로 재정의해준다.
그러면 같은 세션은 같은 `session_group`값을 갖게 된다.
- **SQL에서는 집계 함수(SUM, COUNT, AVG, MAX, MIN 등)를 OVER 절과 함께 사용하면, 윈도우 함수로 동작하게 된다.
이 경우, 특정 그룹 내에서 계산할 수 있다.**


> 로그 내 모든 세션의 시작 시각과 종료 시각을 출력하는 쿼리를 작성해주세요.

> user_pseudo_id - 사용자 아이디 (S3WDQCqLpK)
session_start - 세션 시작 시각
session_end - 세션 종료 시각

➡️ 최종 집계<br>
CTE `session_numbering`에서 `user_pseudo_id`를 추출하고,
각 세션 넘버`session_group` 별로
가장 첫번째 이벤트 시각 `min(event_timestamp_kst)`과
가장 마지막 이벤트 시각 `max(event_timestamp_kst)`을 추출한다.
이후 컬럼 이름을 제시된 대로 맞춰준다.

> 쿼리 결과는 세션 시작 시각 기준으로 정렬되어 있어야 합니다.

➡️ 정렬<br>
2번째 컬럼인 `session_start`를 기준으로 오름차순 정렬한다.


## 코드
```sql
WITH
  sessions as (
    SELECT
      user_pseudo_id,
      event_timestamp_kst,
      lag(event_timestamp_kst) OVER (
        ORDER BY
          event_timestamp_kst
      ) bfr_timestamp
    FROM
      ga
    WHERE
      user_pseudo_id = 'S3WDQCqLpK'
  ),
  session_grouping as (
    SELECT
      *,
      CASE
        WHEN bfr_timestamp IS NULL THEN 1
        WHEN (
          julianday (event_timestamp_kst) - julianday (bfr_timestamp)
        ) * 24 >= 1 THEN 1
        ELSE 0
      END session_group
    FROM
      sessions
  ),
  session_numbering as (
    SELECT
      user_pseudo_id,
      event_timestamp_kst,
      sum(session_group) OVER (
        PARTITION BY
          user_pseudo_id
        ORDER BY
          event_timestamp_kst
      ) session_group
    FROM
      session_grouping
  )
SELECT
  user_pseudo_id,
  min(event_timestamp_kst) session_start,
  max(event_timestamp_kst) session_end
FROM
  session_numbering
GROUP BY
  session_group
ORDER BY 2;
```

## 마무리
확실히 난이도 5 문제를 푸니 어려웠다.
특히 `LAG`, `LEAD` 함수는 처음 접했고, 이를 모르다보니 문제 접근 자체가 쉽지 않았다.
또 집계 함수를 윈도우 함수로 사용할 수 있다는 부분도 처음 알게 되었다!
이렇게 사용한다면 `GROUP BY`를 사용하지 않고도 간단하게 계산이 가능하니 유용할 것 같다.
물론 각각의 장단점이 존재하겠지만 말이다.
