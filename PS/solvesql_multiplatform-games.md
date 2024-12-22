# [solvesql/SQLite] 멀티 플랫폼 게임 찾기

[Velog](https://velog.io/@semoon/solvesqlSQLite-멀티-플랫폼-게임-찾기)

[문제](https://solvesql.com/problems/multiplatform-games/)

✔️ 난이도 3

## 문제 흐름
> 아래 세 플랫폼 계열은 점유율이 높은 메이저 플랫폼 계열입니다.
Sony: 'PS3', 'PS4', 'PSP', 'PSV'
Nintendo: 'Wii', 'WiiU', 'DS', '3DS'
Microsoft: 'X360', 'XONE'
데이터베이스를 조회해 **2012년 이후 출시**된 게임들 중 **둘 이상의 메이저 플랫폼 계열에 출시된 게임** 이름을 출력하는 쿼리를 작성해주세요.
쿼리 결과에는 아래 컬럼만 포함되어 있어야 하고, **중복된 게임은 1번만 출력**되어야 합니다.
- name: 게임 이름

메이저 플랫폼을 걸러내야하니 LEFT JOIN으로 플랫폼 이름을 매칭한 뒤
CASE문을 통해 각 계열사에 넘버링을 하고 컬럼명을 `major_plf`으로 지정했다.
같은 메이저 플랫폼 계열이라면 하나만 체크해야하므로 DISTINCT를 통해 게임별로 같은 플랫폼 계열사 넘버링은 레코드가 하나만 남도록 했다.
그리고 이왕 하는거 2012년 이후 출시된 게임도 필터링했다.

이 서브쿼리에서 다시 메이저 플랫폼이 아닌 게임을 필터링 한 뒤,
게임별로 메이저 플랫폼 계열사의 수가 2 이상인 레코드를 필터링한다.

## 코드
```sql
SELECT
  name
FROM (
  SELECT DISTINCT
    g.name,
    CASE
    WHEN p.name IN ('PS3', 'PS4', 'PSP', 'PSV') THEN 1
    WHEN p.name IN ('Wii', 'WiiU', 'DS', '3DS') THEN 2
    WHEN p.name IN ('X360', 'XONE') THEN 3
    END major_plf
  FROM games g
  LEFT JOIN platforms p USING (platform_id)
  WHERE
    year >= 2012
)
WHERE major_plf IS NOT NULL
GROUP BY 1
HAVING count(*) >= 2;
```
