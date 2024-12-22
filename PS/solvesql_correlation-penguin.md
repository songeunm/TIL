# [solvesql/SQLite] 펭귄 날개와 몸무게의 상관 계수 (+ SQL 제곱 / 루트)

[Velog](https://velog.io/@semoon/solvesqlSQLite-펭귄-날개와-몸무게의-상관-계수-SQL-제곱-루트)

### 📍 SQL에서 제곱과 루트
sql에서 제곱과 루트는 함수를 통해 계산할 수 있다.

#### 💡 `POWER`
```sql
POWER( column_name, exponent )
```
`column_name` 값을 `exponent`승 제곱한 값을 반환한다.
~~꼭 `SQUARE`라는 함수가 존재할것만 같아서 매번 없는 함수를 사용하게 된다.~~

#### 💡 `SQRT`
```sql
SQRT( column_name )
```
Square Root의 약자로 `column_name` 값의 제곱근을 반환한다.

---

[문제](https://solvesql.com/problems/correlation-penguin/)

✔️ 난이도 4

## 문제 흐름
> **날개 길이와 몸무게의 피어슨 상관 계수(Pearson Correlation Coefficient)를 구하는 쿼리**를 작성해주세요.
쿼리 결과에는 아래 컬럼이 포함되어 있어야 하고, 상관 계수는 **소수점 아래 넷째 자리에서 반올림** 해 셋째 자리까지 출력되어야 합니다.
- species: 펭귄 종
- corr: 날개 길이와 몸무게의 상관 계수

어려워보이지만 사실 피어슨 상관 계수라는 말 때문이지 계산만 잘 하면 되는 문제다.
피어슨 상관 계수 공식은 다음과 같다.

<img src=https://velog.velcdn.com/images/semoon/post/887cc00b-01bf-42e6-81e0-6bda1a2ba97d/image.png width=70%>

관측값과 평균의 차를 계산하기 위해 먼저 `avg over`를 통해 각 레코드에 평균 `avg_fl`, `avg_bm`을 계산해 새로운 컬럼으로 붙여준다.
이를 `calc_avg`라는 CTE로 생성했다.

그리고 `calc_avg`로부터 본격적으로 상관계수를 계산한다.
**sql에서 시그마는 `sum`, 제곱은 `power`, 루트는 `sqrt`를 통해 계산할 수 있다.**
계산식을 전부 써준 뒤 `round`함수를 통해 소수점 3째자리까지 반올림하여 나타내도록 하고,
컬럼명을 `corr`으로 지정한다.
계산은 종별로 그룹화하여 진행한다.

## 코드
```sql
WITH
  calc_avg AS (
    SELECT
      species,
      flipper_length_mm,
      avg(flipper_length_mm) OVER (PARTITION BY species) avg_fl,
      body_mass_g,
      avg(body_mass_g) OVER (PARTITION BY species) avg_bm
    FROM penguins
  )

SELECT
  species,
  round(
    sum( (flipper_length_mm - avg_fl) * (body_mass_g - avg_bm))
    / sqrt( sum( power(flipper_length_mm - avg_fl, 2)))
    / sqrt( sum( power(body_mass_g - avg_bm, 2)))
  , 3) corr
FROM calc_avg
GROUP BY species;
```
