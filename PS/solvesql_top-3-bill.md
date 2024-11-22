# [solvesql/SQLite] 레스토랑 요일 별 구매금액 Top 3 영수증 (+ RANK, DENSE_RANK, ROW_NUMBER)

[Velog](https://velog.io/@semoon/solvesqlSQLite-레스토랑-요일-별-구매금액-Top-3-영수증-RANK-DENSERANK-ROWNUMBER)<br>
[문제](https://solvesql.com/problems/top-3-bill/)

✔️ 난이도 4

### 📍 RANK / DENSE_RANK / ROW_NUMBER 의 차이점?
레코드에 순위를 매기고자 할 때 대표적으로 생각나는 세가지 함수이다.
`RANK`, `DENSE_RANK`, `ROW_NUMBER`는 전부 다음과 같은 포맷을 가지며,
특정 기준에 따라 순위를 매길 수 있는 윈도우 함수이다.

윈도우 함수는 특성상 `WHERE`절이나 `HAVING`절에서 쓰일 수 없으므로
이 결과를 이용해서 필터링을 수행해야 하는 경우
1️⃣ 서브쿼리를 이용하거나
2️⃣ CTE(`WITH` 이용)를 이용하여
필터링을 수행할 수 있다.
``` sql
RANK() OVER (PARTITION BY column1 ORDER BY column2)
DENSE_RANK() OVER (PARTITION BY column1 ORDER BY column2)
ROW_NUMBER() OVER (PARTITION BY column1 ORDER BY column2)
```
이들의 각각의 세부사항과 차이점에 대하여 알아보자.

#### 💡 RANK
- **동일한 값에 동일한 순위**를 부여
- 동일 순위가 여러 개인 경우 **건너뛴 순위 발생**
![](https://velog.velcdn.com/images/semoon/post/a9676567-b5b9-46a7-8d61-019cbd9973d0/image.png)

#### 💡 DENSE_RANK
- **동일한 값에 동일한 순위**를 부여
- 동일 순위가 여러 개인 경우에도 **순위를 건너뛰지 않음**
![](https://velog.velcdn.com/images/semoon/post/0897b8d1-f46a-4ce5-801c-fa5e050fa436/image.png)

#### 💡 ROW_NUMBER
- **동일한 값에도 순차적인 순위**를 부여 -> 무조건 고유한 순위 부여
![](https://velog.velcdn.com/images/semoon/post/079001d6-4303-4bbc-8a55-f9923447124a/image.png)

### 📍 유사한 함수
#### 💡 NTILE
- `NTILE(n) OVER (PARTITION BY column1 ORDER BY column2)`
- 레코드를 N개 구간으로 나누어 각 행에 구간 번호를 부여
- **데이터를 균등하게 분할**하거나 구간별 분석에 사용


## 문제 흐름

> 요일 별 결제 금액으로 Top 3 를 지불한 영수증을 출력해주세요. 결제 금액은 total_bill 컬럼에 있습니다.

➡️ 계산 내용
`day`별로 `total_bill`를 정렬하여 그 순위가 3위인 것까지만 각각 추출한다.

> 예를 들어 금요일 손님들의 결제 금액이 각각 $10, $9, $9, $8, $5, $2 였다면 상위 결제 금액 3개는 $10, $9, $8입니다. 따라서 결제 금액이 $10, $9, $9, $8인 총 4개의 영수증을 각각 출력해야 합니다.

➡️ 계산 세부 사항
동일한 값에 대해서 같은 순위를 부여해야한다.
따라서 이에 적합하게 사용할 수 있는 `DENSE_RANK`를 이용하여 순위를 매긴 뒤,
다시 그 결과에서 필요한 컬럼들을 추출하고 `WHERE`절을 통해 3 이상의 순위만 추출한다.

## 코드
```sql
SELECT day, time, sex, total_bill
FROM (SELECT *, dense_rank() over(PARTITION BY day ORDER BY total_bill DESC) d_rank
      FROM tips)
WHERE d_rank <= 3;
````
