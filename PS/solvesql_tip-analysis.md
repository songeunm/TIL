# [solvesql/SQLite] 레스토랑 웨이터의 팁 분석

[Velog](https://velog.io/@semoon/solvesqlSQLite-레스토랑-웨이터의-팁-분석)<br>
[문제](https://solvesql.com/problems/tip-analysis/)

✔️ 난이도 2

## 문제 흐름
> 구체적으로는 각 요일의 시간대별 평균 팁은 얼마인지, 평균 일행 수는 몇 명인지 확인하고자 합니다.

➡️ 요일*`day`*, 시간대*`time`*를 `GROUP BY`하여 팁 *`tip`*과 일행 수 *`size`*의 `AVG` 함숫값을 구한다.

> 평균 팁과 평균 일행 수는 소수점 아래 셋째 자리에서 반올림 해 둘째 자리까지 출력하고,

➡️ `AVG` 함숫값을 `ROUND` 함수를 통해 소숫점 둘째 자리까지 나타내도록 한다.

> 결과 데이터가 요일, 시간대의 알파벳 순으로 정렬되도록 쿼리를 작성해주세요.

➡️ *`day`*와 *`time`*을 기준으로 `ORDER BY`하여 정렬한다.

그리고 컬럼명은 문제에서 주어진대로 지어주었다.

## 코드
```sql
SELECT day, time, round(avg(tip), 2) avg_tip, round(avg(size), 2) avg_size
FROM tips
GROUP BY day, time
ORDER BY 1, 2;
```

## 마무리
내가 쿼리 작성에서 부족한 부분은 쿼리 최적화라든지, 잘 써보지 않은 `PARTITION BY`라든지...
그런 류인데 일단 프로그래머스에서 여러 문제를 많이 풀어봤으니 조금 더 난이도를 높게 잡고 해봐도 될 것 같다.
어려운 걸 해야 공부도 하고! 그럴듯
