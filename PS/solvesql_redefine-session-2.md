# [solveslq/SQLite] 세션 유지 시간을 10분으로 재정의하기

[Velog](https://velog.io/@semoon/solveslqSQLite-세션-유지-시간을-10분으로-재정의하기)

[문제](https://solvesql.com/problems/redefine-session-2/)

✔️ 난이도 5

## 문제 흐름
> 세션을 종료하는 기준을 사용자가 10분 이상 행동하지 않을 때로 수정하여, 사용자 'a8Xu9GO6TB’의 세션을 재정의해 세션 ID를 계산하는 쿼리를 작성해주세요.
재정의한 세션 ID는 1부터 시작해 세션 시작 시간이 빠른 순서대로 1씩 증가하는 자연수여야하고 new_session_id 컬럼에 출력되어야 합니다.
결과 데이터는 아래 컬럼을 포함해야 하고, 이벤트 발생 시각이 빠른 순서대로 정렬되어야 합니다.
- user_pseudo_id: 사용자 아이디 (’a8Xu9GO6TB’)
- event_timestamp_kst: 이벤트가 발생한 시각
- event_name: 이벤트 이름
- ga_session_id: 기존 세션 아이디
- new_session_id: 재정의한 세션 아이디

`ga` 테이블의 `event_timestamp_kst` 컬럼의 **이전 레코드와의 차이**를 구해야 로그 텀을 알 수 있다.
이를 위해서 **`lag` 함수**를 이용해 `event_timestamp_kst`를 기준으로 오름차순 정렬하여 이전 `event_timestamp_kst`의 값을 가져오도록 하고 `prev_timestamp`라는 컬럼으로 만들었다.
필요한 컬럼들을 모두 포함시켜주고, 'a8Xu9GO6TB' 사용자에 대한 새로운 세션만 계산하면 되므로 여기서 필터링해줬다.
이 컬럼을 사용해 계산을 진행하기 위해 이를 cte `calc_prev_timestamp`로 만들었다.

이제 메인 쿼리에서 새로운 세션을 계산해주면 된다.
`prev_timestamp`와 `event_timestamp_kst`의 차를 `julianday`를 이용해 계산한 뒤,
`CASE`문을 통해 만약 이 **차이가 10분 미만**이라면 세션 시작이 아니므로 **0**,
`prev_timestamp`가 NULL이어서 계산값이 **NULL이거나
차이가 10분 이상**이라면 새로운 세션이 시작됨을 알리기 위해 **1**을 반환하도록 했다.

여기서 차이를 `julianday`를 이용해 계산했으므로 일수가 반환되는데,
10분이 일수로 만들어주든가 계산한 일수를 분단위로 만들어주든가 하는 과정이 필요하다.
여기서 일수를 분으로 만든다면 정수만 곱해주면 돼서 딱히 상관 없지만
분을 일으로 만들게 된다면 **type casting에 주의**해야한다.
`10/60/24`와 같이 단순히 계산하면 정수형 계산으로 0이 나올것이기 때문..!

이를 `sum over`를 `event_timestamp_kst` 순서대로 누적합을 계산하여 `new_session_id`를 생성했다.
필요한 컬럼을 모두 포함시켜 준 뒤, 2번 컬럼 (`event_timestamp_kst`)를 기준으로 오름차순 정렬해주면 된다.

## 코드
```sql
WITH
  calc_prev_timestamp AS (
    SELECT
      user_pseudo_id,
      event_timestamp_kst,
      lag(event_timestamp_kst, 1) OVER (PARTITION BY user_pseudo_id ORDER BY event_timestamp_kst) prev_timestamp,
      event_name,
      ga_session_id
    FROM ga
    WHERE user_pseudo_id = 'a8Xu9GO6TB'
  )

SELECT
  user_pseudo_id,
  event_timestamp_kst,
  event_name,
  ga_session_id,
  sum( CASE
      WHEN julianday(event_timestamp_kst) - julianday(prev_timestamp) < 10.0/60/24 THEN 0
      ELSE 1 END
  ) OVER (ORDER BY event_timestamp_kst) new_session_id
FROM calc_prev_timestamp
ORDER BY 2;
```
