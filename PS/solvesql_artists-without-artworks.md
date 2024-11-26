# [solvesql/SQLite] 작품이 없는 작가 찾기

[Velog](https://velog.io/@semoon/solvesqlSQLite-작품이-없는-작가-찾기)<br>
[문제](https://solvesql.com/problems/artists-without-artworks/)

✔️ 난이도 3

## 문제 흐름
먼저 말해보자면 두가지 방법으로 해결했다!
간단한 문제인 만큼 간단한 풀이들이지만
문득 *어떤 풀이가 현업에서의 데이터 크기에서도 무리 없이 작동할 수 있을 만큼 효율적일까?* 하는 생각이 들었다.
애초에 첫번째 풀이가 비효율적이라는 생각이 들어서 두번째 풀이를 생각해낸 것이기도 하다.
일단 두가지 풀이에 대해서 알아보자.
![](https://velog.velcdn.com/images/semoon/post/7d256d5c-32d4-4172-8bd6-817102c95653/image.png)
#### 👀 문제 분석
> MoMA에 등록되어있고, 현재 살아있지 않은 작가 중 MoMA에 등록된 작품이 없는 작가의 ID와 이름을 출력하는 쿼리를 작성해주세요.

➡️ 필터링 조건
조건은 세가지가 있다.

1. MoMA에 등록되어있고, 2. 현재 살아있지 않으며, 3. MoMA에 등록된 작품이 없는.

1.은 `artists` 테이블을 베이스로 작업해야겠다고 생각되었다.
2.는 `artists` 테이블에서 `death_year` 컬럼 값이 `NULL`이 아니다, 로 알 수 있다.
3.은 `artworks_artists` 테이블에 존재하지 않는 `artist_id`, 로 볼 수 있다.

여기서 2.는 그냥 `WHERE`절을 통해 조건을 걸면 되지만
3.은 다른 테이블에서 비교 값을 가져와야하기 때문에 방법이 갈린다.

#### 💡 solution 1
- `artists` 테이블에 `LEFT JOIN`을 통해 `artworks_artists` 테이블을 `airtist_id`를 key로 붙여준다.

이렇게 하게 되면 MoMA에 등록된 작품이 없는 작가의 경우 `artwork_artists`와 `artist_id`가 매칭되지 않을 것이기 때문에 `artwork_artists` 테이블의 컬럼값이 `NULL`로 남게된다.
그럼 이 조인 결과에서 `artwork_artists` 컬럼값이 `NULL`인 레코드만 필터링하면 3.을 만족하게 된다.

#### 💡 solution 2
- 서브쿼리를 통해 `artworks_artists` 테이블에서 모든 `artists_id`를 중복을 제거해 추출한 값을 구한다.

`artworks_artists` 테이블에 `artist_id`가 존재하는 작가는 MoMA에 등록된 작품이 있는 작가이다.
이 작가들의 id를 `DISTINCT`를 통해 중복을 제거한 명단을 만드는 거다.
그러고 `artists` 테이블에서 이 서브쿼리 결과값에 `artists_id`가 포함되는지 여부를 `WHERE`절을 통해 조건으로 걸어주면 3.을 만족하게 된다.

#### 🧐 효율적인 코드?
물론 데이터가 작을 경우 둘의 성능은 비등비등 하다.
하지만 내가 생각한건 **"현업에서"**라는 조건이 붙은 상황이었다.

아무래도 solution 1이 `JOIN`을 사용하여 테이블의 모든 레코드를 조인한 다음 조건에 따라 필터링을 진행하기 때문에 비효율적일거라는 생각이 들었다.
`JOIN` 자체도 다른 연산이나 작업보다 시간이 오래 걸린다고 알고있기도 했다.
그래서 잦은 `JOIN`을 피하기 위해 DW에서 분석에 자주 사용되는 테이블을 미리 조인해놓거나, Data Mart를 구축하기도 하고 그렇다고 알고있었다.
또 두 테이블의 조인 결과를 일단 생성해야 하므로 데이터가 커질 경우, 굉장히 큰 조인 결과가 메모리에 로드되며 메모리와 디스크 I/O가 증가할 수 있다.

하지만 gpt에게 물어봤더니 예상 외로 최적화를 위한 적절한 인덱스가 설정되어있다는 가정 하에
`JOIN`을 이용하는 것이 더 효율적이라는 답변을 받았다.
`JOIN`을 하며 미리 생각한 단점들이 존재하지만 적절한 인덱스를 통해 빠르게 작동하고, 데이터베이스 엔진이 조인을 최적화할 가능성이 크다고 한다.

또 수정 제안 사항 중 solution 2는 `NOT IN`을 사용하고 있는데,
이 경우 서브쿼리의 결과에 `NULL`값이 포함될 때 비교 결과가 항상 `False`가 되는 점을 들었다.
다행히 이 문제에서는 서브쿼리 결과에 `NULL`값이 없었지만! (아무래도 id이다보니 pk가 적용되어 잘 관리된 케이스일 수도 있다.)
앞선 경우를 피하려면 `NOT EXISTS`를 대안으로 사용하는 것을 추천했다.

`NOT EXISTS`를 사용하면 solution 2를 아래와 같이 수정할 수 있다.
```sql
-- solution 2 (modify)
SELECT artist_id, name
FROM artists a
WHERE
  NOT EXISTS (
  	SELECT 1
    FROM artworks_artists aa
    WHERE a.artist_id = aa.artist_id
  )
  AND death_year IS NOT NULL;
```
`NOT EXISTS`는 서브쿼리가 특정 조건을 만족하는 행을 반환하지 않을 때 조건을 참으로 판단한다.
이렇게 쿼리를 작성하면 `JOIN`을 하지 않음과 중복된 계산이 적다는 점을 장점으로 가져가면서 `NOT IN`을 사용할 경우 발생 가능할 문제점도 막을 수 있으면서,
데이터셋이 클 경우 가장 효율적으로 작동할 가능성이 높다고 한다.

## 코드
```sql
-- solution 1
SELECT a.artist_id, name
FROM artists a
LEFT JOIN artworks_artists aa
ON a.artist_id = aa.artist_id
WHERE
  artwork_id IS NULL
  AND death_year IS NOT NULL;

-- solution 2
SELECT artist_id, name
FROM artists
WHERE
  artist_id NOT IN (SELECT DISTINCT artist_id FROM artworks_artists)
  AND death_year IS NOT NULL;
```
