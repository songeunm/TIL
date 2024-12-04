# [solvesql/SQLite] 제목이 모음으로 끝나지 않는 영화 (+ SUBSTR/SUBSTRING, REGEXP)

[Velog](https://velog.io/@semoon/solvesqlSQLite-제목이-모음으로-끝나지-않는-영화-SUBSTRSUBSTRING-REGEXP)<br>
[문제](https://solvesql.com/problems/film-ending-with-consonant/)

✔️ 난이도 1

### 📍 SUBSTR, SUBSTRING ?
문자열에서 부분 문자열을 추출할 때 사용 가능한 함수이다.

#### ✅ `SUBSTR`
```sql
SUBSTR(string, start)
```
`start`가 양수일 경우 `string`의 왼쪽에서 `start`개의 문자를 추출하고,
`start`가 음수일 경우, `string`의 오른쪽에서 `abs(start)`(절댓값)개의 추출한다.
- Ex: `SUBSTR('hello', -3)` -> 오른쪽부터 3개의 문자를 추출 -> `llo`
- 지원 RDBMS: SQLite, Oracle, PostgreSQL, MySQL, MariaDB, DB2 등
- 단, SQLite의 경우 `SUBSTRING`의 문법과 같이 사용한다.

#### ✅ `SUBSTRING`
```sql
SUBSTRING(string, start, length)
-- 표준 SQL 문법
SUBSTRING(string FROM start FOR length)
```
`string`에서 `start`번째의 문자부터 `length`개의 문자를 추출한다.
- Ex: `SUBSTRING('hello', 2, 3)` -> 2번째 문자부터 3개의 문자 추출 -> `ell`
- **표준 SQL에 해당한다.**
- 지원 RDBMS: PostgreSQL, MySQL, MariaDB, SQL Server, Teradata 등

### 📍 REGEXP란?
```sql
SELECT *
FROM table_name
WHERE column_name REGEXP 'pattern';
```
정규 표현식을 사용해 문자열 패턴을 매칭한다.
- 패턴은 다음을 조합해서 작성할 수 있다.
<img src = https://velog.velcdn.com/images/semoon/post/f1f239e6-33da-43c5-8cae-2d6dbeaf4c74/image.png>
- 기본적으로 **대소문자를 구분**하나 RDBMS에 따라 구분하지 않는 경우도 있다.
- `0-9`
- **복잡한 패턴을 사용할 경우 성능에 영향을 미칠 수 있다.**
- Ex: `SELECT * FROM table_name WHERE column_name REGEXP '^abc';` -> `column_name`이 `abc`로 시작하는 레코드 추출
- 지원 RDBMS: MySQL(8.0 이상), PostgreSQL, Oracle 등

---

## 문제 흐름
> 17세 미만 학생이 DVD 대여점에 혼자와서는 대여할 수 없는 영화 중 제목이 모음(’A’, ‘E’, ‘I’, ‘O’, ‘U’)로 끝나지 않는 영화를 찾고 싶습니다. 

> G (General Audience): 모든 연령 관람 가능
PG (Parental Guidance Suggested): 아동 관람 시 부모 지도 필요
PG-13 (Parents Strongly Cautioned): 13세 미만 관람 시 부모의 주의 필요
R (Restricted): 17세 미만은 부모 또는 21세 이상과 동반 관람만 가능
NC-17 (No One 17 and Under Admitted): 18세 미만 관람 불가

1. 17세 미만 학생이 혼자 대여할 수 없는 `R`또는 `NC-17`등급의 영화이면서
2. 영화 제목이 모음으로 끝나는 영화를 필터링한다.

이 두 조건 모두 `WHERE`에서 필터링 가능한데, 1의 경우 조건이 `OR`로 묶여있고, 2와는 `AND`로 묶어줘야한다.
**`AND`는 `OR`보다 우선순위가 높기 때문에** 1의 조건을 괄호로 묶어 먼저 계산되도록 명시해야한다.

여기서 2의 조건식을 작성하는 방법은 3가지로 구현했다.


#### 💡 solution 1: `LIKE`를 통해 작성
가장 익숙하게 사용해오던 방법이다. 그리고 가장 단순하다.
`LIKE`를 통해 각 모음에 대해 해당 알파멧으로 끝나는지 여부를 작성해 `AND`로 묶어준다.

PostgreSQL의 경우 `ILIKE`라는 대소문자를 구분하지 않고 탐색하는 함수를 지원하나
SQLite은 지원하지 않으며 `LIKE`에서 대소문자 구분하지 않고 탐색한다.
MySQL도 `ILIKE`를 지원하지 않으나 `LIKE`가 대소문자를 구분하니 주의해야한다.

#### 💡 solution 2: `SUBSTR`과 `IN`을 통해 작성 
`substr` 함수를 `title`의 가장 마지막 알파벳을 가져오고, `NOT IN`으로 모음 알파벳중 하나인지 체크한다.

MySQL, SQL Server에서는 오른쪽에서부터 특정 길이의 문자열을 반환하는 `RIGHT` 함수를 지원한다.
비슷하게 왼쪽에서부터 특정 길이의 문자열을 반환하는 `LEFT`함수 또한 지원한다.
문자열로 작성된 datetime 포맷에서 연도 등을 추출할 때 단순하게 사용하기 좋아 자주 사용했던 함수다.
다만 표준 SQL은 아니다.
SQLite에서는 지원하지 않는다. 대신 부분 문자열을 반환하는 `SUBSTR` 함수를 사용할 수 있다.
`LIKE`와 달리 대소문자를 명시해줘야 한다.
데이터를 뽑아 확인하면 알 수 있듯이 모든 `title`은 대문자로만 이루어져있다.
때문에 모음 알파벳은 모두 대문자로만 작성해주면 된다.

#### 💡 solution 3: `REGEXP`를 통해 작성
`regexp`를 통해 모음 알파벳들중 하나가 포함되는지 여부를 체크한다.
다만 SQLite는 정규표현식을 지원하지 않기 때문에 코드 작성 후 정답 여부는 확인해보지 못했다.
정규표현식은 정말 자주, 많이 사용해보지 않으면 외우기가 쉽지 않을 것 같기도 하다.
패턴을 참고해서 작성을 잘 할수만 있다면 괜찮을 것 같다.

## 코드
```sql
-- solution 1 
SELECT title
FROM film
WHERE
  (rating = 'R'
  OR rating = 'NC-17')
  AND title NOT LIKE '%a'
  AND title NOT LIKE '%e'
  AND title NOT LIKE '%i'
  AND title NOT LIKE '%o'
  AND title NOT LIKE '%u';
  
-- solution 2
SELECT title
FROM film
WHERE
  (rating = 'R'
  OR rating = 'NC-17')
  AND substr(title, -1, 1) NOT IN ('A', 'E', 'I', 'O', 'U');
  
-- solution 3
 SELECT title
FROM film
WHERE
  (rating = 'R'
  OR rating = 'NC-17')
  AND title NOT REGEXP '[aeiou]$';
```
