# [solvesql/SQLite] 전국 카페 주소 데이터 정제하기 (+ INSTR)

[Velog](https://velog.io/@semoon/solvesqlSQLite-전국-카페-주소-데이터-정제하기-INSTR)

### 📍`INSTR`
```sql
INSTR( main_str, search_str )
```
[SQLite 공식 문서 Built-In Scalar SQL Functions](https://www.sqlite.org/lang_corefunc.html#instr)
`main_str`에서 `search_str`가 처음 나타나는 위치를 찾아 그 인덱스를 반환한다.
만약 없다면, 0을 반환한다.
만약 `main_str`과 `search_str`가 NULL이라면, NULL을 반환한다.

---

[문제](https://solvesql.com/problems/refine-cafe-address/)

✔️ 난이도 3

## 문제 흐름
> address 컬럼에는 주소 정보가 들어가있는데, 정제가 되어있지 않아 활용이 어렵습니다. 이 데이터를 각각 시, 도 정보를 담고 있는 sido 컬럼과 시, 군, 구 정보를 담고 있는 sigungu로 정제하여 각 행정구역 별로 몇 개의 카페가 있는지 집계하는 쿼리를 작성해주세요.
쿼리 결과는 카페가 가장 많은 행정구역 순으로 출력을 해야 합니다.
- sido: 시, 도 정보 (예시. ‘대전광역시’, ‘강원특별자치도’, ‘충청남도’)
- sigungu: 시, 군, 구 정보 (예시. ‘성남시’, ‘성동구’)
- cnt: 해당 행정구역에 포함된 카페 수

![](https://velog.velcdn.com/images/semoon/post/93de9f69-61c0-4138-b4ea-825c700f56a8/image.png)

`cafes` 테이블의 레코드를 일부 뽑아 확인해보면 `address`컬럼이 띄어쓰기로 구분된 문자열임을 알 수 있다.
저 **띄어쓰기를 기준으로 문자열을 잘라낼 수 있다면** 문제의 조건을 만족시킬 수 있다.
python의 `.split()`이 너무 그리웠지만.... 찾아보니 sql에는 딱히 이런 함수는 없는듯 했다.

그리고 생각했던게 *"문자열에서 스페이스(공백)의 인덱스를 찾아주는 함수는 있을 것 같다"* 였다.
공식 문서에서 함수들을 찾아본 결과 존재했다!
이와 `substr`을 이용해서 `address`에서 스페이스 이전까지의 문자열을 추출해 `sido`컬럼으로 만들었다.
이 시도 부분을 제외한 문자열에서 다시 이전 작업을 반복해야 시군구 파트를 얻을 수 있으므로 시도 부분을 제외한 문자열을 `exclude_sido` 컬럼으로 만들어주고,
나중에 행정구역별 카페 수를 집계해야하므로 `cafe_id`컬럼도 함께 포함시켰다.
`WITH`를 통해 이를 cte로 만들었다.

다시 이 cte에서 같은 작업을 반복하여 시군구파트를 추출하고, `cafe_id`를 `count`함수를 통해 중복을 제거하여 집계했다.

## 코드
```sql
WITH
  cte AS (
    SELECT
      cafe_id,
      substr(address, 1, instr(address, ' ') - 1) sido,
      substr(address, instr(address, ' ') + 1, length(address)) exclude_sido
    FROM cafes
  )

SELECT
  sido,
  substr(exclude_sido, 1, instr(exclude_sido, ' ') - 1) sigungu,
  count(DISTINCT cafe_id) cnt
FROM cte
GROUP BY 1, 2
ORDER BY 3 DESC;
```
