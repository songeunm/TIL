# [solvesql/SQLite] 유량(Flow)와 저량(Stock)

[Velog](https://velog.io/@semoon/solvesqlSQLite-유량Flow와-저량Stock)

[문제](https://solvesql.com/problems/flow-and-stock/)

✔️ 난이도 4

## 문제 흐름
> ‘연도별로 새롭게 소장하게 된 작품의 수’와 같이 일정 기간 동안 측정되는 지표를 ‘유량(Flow) 지표’라고 하고, ‘누적 소장 작품 수’와 같이 특정 시점에 측정되는 지표를 ‘저량(Stock) 지표’라고 합니다. 
미술관의 소장 규모를 파악하기 위해 **연도별로 새롭게 소장하게 된 작품의 수와, 연도별 누적 소장 작품 수를 계산하는 쿼리를 작성해주세요. **
**저량 지표에 변화가 없는 연도는 출력되지 않아야** 하고, **소장 년도 정보가 없는 작품은 집계에서 제외**해주세요. 
결과 데이터에는 아래 세 개 컬럼이 포함되어야 하며, **Acquisition year 컬럼을 기준으로 오름차순 정렬**되어 있어야 합니다.
- Acquisition year: 소장 연도
- New acquisitions this year (Flow): 해당 연도에 새롭게 소장하게 된 작품의 수
- Total collection size (Stock): 해당 연도까지 누적 소장 작품 수

"연도별"이기 때문에 소장 일자 `acquisition_date`로부터 `substr`을 이용해 앞에서 4자리만 추출해 `Acquisition year`로 만든다.
소장 연도별 `artwork_id`를 중복을 제거하여 카운트하면 `New acquisitions this year (Flow)`가 된다.
다음, `Total collection size (Stock)`은 `New acquisitions this year (Flow)`를 `sum over`를 통해 `Acquisition year`를 기준으로 정렬하여 누적합을 계산하면 된다.

저량 지표에 변화가 없는 연도는 출력하지 말라 했으므로 `New acquisitions this year (Flow)`가 0인 연도는 제외하고,
소장 년도 정보가 없는 작품은 집계에서 제외하라 했으므로 `Acquisition year`가 NULL인 경우도 제외한다.

첫번째 컬럼인 `Acquisition year`를 기준으로 오름차순 정렬하면 끝이다!

## 코드
```sql
SELECT
  substr(acquisition_date, 1, 4) "Acquisition year",
  count(DISTINCT artwork_id) "New acquisitions this year (Flow)",
  sum(count(distinct artwork_id)) OVER (ORDER BY substr(acquisition_date, 1, 4)) "Total collection size (Stock)"
FROM artworks
WHERE substr(acquisition_date, 1, 4) IS NOT NULL
GROUP BY 1
HAVING count(DISTINCT artwork_id) > 0
ORDER BY 1;
```
