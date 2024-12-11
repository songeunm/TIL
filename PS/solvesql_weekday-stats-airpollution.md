# [solvesql/SQLite] 서울숲 요일별 대기오염도 계산하기

[Velog](https://velog.io/@semoon/solvesqlSQLite-서울숲-요일별-대기오염도-계산하기)<br>
[문제](https://solvesql.com/problems/weekday-stats-airpollution/)

✔️ 난이도 3

## 문제 흐름
> measurements 테이블의 데이터를 조회하여 요일별 대기 오염도 평균을 계산하는 쿼리를 작성해주세요.
- weekday: 요일 (월요일-일요일)

➡️ 집계 내용

"요일별"을 계산헤야하는데, 이 테이블에는 date 타입의 `measured_at` 컬럼만 있다.
이를 통해 요일으로 변형해야한다.
이 때 사용할 수 있는 함수가 `STRFTIME`이다. 다음과 같이 사용할 수 있다.
표현 형식은 아래 표와 마지막에 첨부하는 공식문서 링크를 참조하면 된다!
```sql
STRFTIME([표현 형식], [날짜])
```
|표현 형식|변환 결과|
|---|---|
|%d		|day of month: 01-31|
|%e		|day of month without leading zero: 1-31|
|%f		|fractional seconds: SS.SSS|
|%F		|ISO 8601 date: YYYY-MM-DD|
|%G		|ISO 8601 year corresponding to %V|
|%g		|2-digit ISO 8601 year corresponding to %V|
|%H		|hour: 00-24|
|%I		|hour for 12-hour clock: 01-12|
|%j		|day of year: 001-366|
|%J		|Julian day number (fractional)|
|%k		|hour without leading zero: 0-24|
|%l		|%I without leading zero: 1-12|
|%m		|month: 01-12|
|%M		|minute: 00-59|
|%p		|"AM" or "PM" depending on the hour|
|%P		|"am" or "pm" depending on the hour|
|%R		|ISO 8601 time: HH:MM|
|%s		|seconds since 1970-01-01|
|%S		|seconds: 00-59|
|%T		|ISO 8601 time: HH:MM:SS|
|%U		|week of year (00-53) - week 01 starts on the first Sunday|
|%u		|day of week 1-7 with Monday==1|
|%V		|ISO 8601 week of year|
|%w		|day of week 0-6 with Sunday==0|
|%W		|week of year (00-53) - week 01 starts on the first Monday|
|%Y		|year: 0000-9999|
|%%		|%|

하지만 월요일-일요일 으로 변환해주는 함수는 없다.
그러니 요일에 맞춰 숫자로 변환해주는 `%u`나 `%w`에 `CASE`문을 적용해 변환해줘야한다.
`CASE`문의 조건만 잘 맞춰 요일로 변환한다면 어떤걸 사용하든 상관은 없다.

나머지 컬럼은 첫번째 컬럼을 기준으로 그룹화하고 `avg`함수를 통해 평균값을 계산한다.


> 대기 오염도 값은 모두 소수점 다섯째 자리에서 반올림 해 넷째 자리까지 표현되어야 합니다. 또한, 쿼리 결과는 월요일부터 일요일 순으로 출력되어야 합니다.

➡️ 데이터 포맷팅, 정렬

`round`함수를 통해 집계값들을 소수점 4번째 자리까지 나타내도록 한다.
정렬은 다시 `strftime`함수를 이용하면 되는데,
이때 월요일 - 일요일 순으로 출력되어야 하므로 월요일을 1으로 하는 `%u` 포맷을 사용해야한다.
통일성을 주기 위해 컬럼 선택을 할 때도 `%u`를  사용했다.

## 코드
```sql
SELECT
  CASE strftime('%u', measured_at)
    WHEN '1' THEN '월요일'
    WHEN '2' THEN '화요일'
    WHEN '3' THEN '수요일'
    WHEN '4' THEN '목요일'
    WHEN '5' THEN '금요일'
    WHEN '6' THEN '토요일'
    WHEN '7' THEN '일요일'
    ELSE '알 수 없는 요일'
  END weekday,
  round(avg(no2), 4) no2,
  round(avg(o3), 4) o3,
  round(avg(co), 4) co,
  round(avg(so2), 4) so2,
  round(avg(pm10), 4) pm10,
  round(avg(pm2_5), 4) pm2_5
FROM measurements
GROUP BY 1
ORDER BY strftime('%u', measured_at);
```
## 마무리
변환 함수를 찾아보면서 [SQLite 공식 문서의 DATE/TIME 함수 파트](https://www.sqlite.org/lang_datefunc.html)를 읽었다.
`date`와 `time` 타입에 관한 함수들은 왜인지 더 기억하기가 쉽지 않은데, 한번 쭉 읽어보면 좋을 것 같다.
