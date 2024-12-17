# [solvesql/SQLite] 전력 소비량 이동 평균 구하기 (+ OVER(ROWS) )

[Velog](https://velog.io/@semoon/solvesqlSQLite-전력-소비량-이동-평균-구하기-OVERROWS)

### 📍 OVER에서 ROWS의 사용
`OVER`에서 `PARTITION BY`를 통한 그룹화, `ORDER BY`를 통한 정렬만 있는 줄 알았는데, `ROWS`를 통해서 특정 범위 내의 레코드를 지정하여 윈도우를 구성할 수 있었다.
아래를 통해 자세히 알아보자.

#### 💡 기본 구조
```sql
ROWS BETWEEN start AND end
```
- `start`부터 `end`까지의 레코드들으로 윈도우를 구성

#### 💡 범위 설정 키워드
`start`와 `end`에 사용될 수 있는 키워드

- `CURRENT ROW`: 현재 레코드를 가리킬 때 사용
- `n PRECEDING`: n개 이전 레코드에서 현재 레코드까지를 가리킬 때 사용 (이전 n개)
- `n FOLLOWING`: 현재 레코드에서 n개 이후 레코드까지를 가리킬 때 사용 (이후 n개)
- `UNBOUNDED PRECEDING`: 처음부터 현재 레코드까지를 가리킬 때 사용 (처음부터 지금까지)
- `UNBOUNDED FOLLOWING`: 현재 레코드부터 마지막까지 가리킬 때 사용 (지금부터 끝까지)

`PRECEDING`은 start에 들어가고, `FOLLWING`은 end에 들어갈 수 있음
이러한 범위 설정은 이동평균, 누적합, 특정 구간의 데이터 계산 등 다양한 분석에 유용하게 사용 가능

---

[문제](https://solvesql.com/problems/moving-average-of-power-consumption/)

✔️ 난이도 4

## 문제 흐름
> **2017년 1월 1일 0시부터 2017년 2월 1일 0시까지 10분 단위로 3개 발전소 전력 소비량의 1시간 범위 단순 이동 평균을 계산**하는 쿼리를 작성해주세요.
평균을 내기 위한 데이터의 범위가 1시간보다 작은 경우 **해당 범위에 포함되는 측정 값만 평균 계산에 포함**시켜주세요.
쿼리 결과에는 아래 컬럼이 포함되어 있어야 하고, 이동 평균 값은 소수점 셋째 자리에서 반올림 해 **둘째 자리까지 표시**되어야 합니다.
- end_at: 이동 평균 범위의 끝 시각
- zone_quads: Quads 지역 전력 소비량의 1시간 단순 이동 평균
- zone_smir: Smir 지역 전력 소비량의 1시간 단순 이동 평균
- zone_boussafou: Boussafou 지역 전력 소비량의 1시간 단순 이동 평균

이동 평균 문제다.
이동 평균은 학부시절 수치해석 강의에서 배운적이 있다.
모르는 분들이 있다면 아래 그림이 이해기 쉬울 것이다.

![](https://velog.velcdn.com/images/semoon/post/e46d467f-77fd-4ac8-921c-41919767f670/image.png)

10분 간격으로 1시간짜리 윈도우를 이동해가며 그 평균을 구하는 것이다.
아무튼 이는 **`avg() OVER`**를 이용해서 구할 수 있다.
WHERE를 통해 2017년 1월 1일 0시부터 2017년 2월 1일 0시까지로 필터링한 뒤
10분 간격이고, 컬럼 설명을 보면 `end_at`이 이동 평균 범위의 **끝 시각**을 나타내므로
5개 이전 레코드부터 현재 레코드까지로 윈도우를 잡고 `avg`를 계산한다.
이미 정렬이 되어있지만, 생각대로 정확하게 윈도우를 지정하기 위해 `measured_at`을 기준으로 오름차순 정렬해줬다.
또한 끝 시각이기 때문에 `measured_at`에 10분을 더해줬다.

## 코드
```sql
SELECT
  datetime(measured_at, '+10 minutes') end_at,
  round(avg(zone_quads) OVER (ORDER BY measured_at ROWS BETWEEN 5 PRECEDING AND CURRENT ROW), 2) zone_quads,
  round(avg(zone_smir) OVER (ORDER BY measured_at ROWS BETWEEN 5 PRECEDING AND CURRENT ROW), 2) zone_smir,
  round(avg(zone_boussafou) OVER (ORDER BY measured_at ROWS BETWEEN 5 PRECEDING AND CURRENT ROW), 2) zone_boussafou
FROM power_consumptions
WHERE measured_at BETWEEN '2017-01-01 00:00:00' AND '2017-02-01 00:00:00';
```
