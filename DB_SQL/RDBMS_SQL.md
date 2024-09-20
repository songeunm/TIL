[Velog](https://velog.io/@semoon/RDBMS%EC%99%80-SQL)

## 관계형 데이터베이스 (RDBMS)

구조화된 데이터를 저장하고 질의할 수 있도록 해주는 스토리지

- 스키마
테이블을 구성하는 컬럼의 이름, 타입 등

- **프로덕션 데이터베이스**
    - **OLTP** (OnLine Transaction Processing)
    - MySQL, PostgreSQL, Oracle 등
    - 서비스에 필요한 정보 저장 → **빠른 속도**
    - BE·FE 개발자가 주로 사용
    - PK uniqueness 보장
- **데이터 웨어하우스**
    - **OLAP** (OnLine Analytical Processing)
    - Redshift, Snowflake, BigQuery, Hive 등
    - 데이터 분석·모델 빌딩 등에 필요한 정보 저장 → **큰 처리 크기**
    - 데이터 팀이 주로 사용
    - Production DB를 복사해 DW에 저장
    큰 쿼리를 Production DB로 날리면 Production DB가 느려질 수 있음
    - PK uniqueness 보장 X

- 관계형 데이터베이스 구조
    - 데이터베이스(스키마)
        - 테이블을 포함하는 폴더
        - raw_data, analytics, PII 등
    - 테이블
        - 스키마 (테이블 스키마)
        테이블의 구조
        - 테이블 → 레코드(로우)로 구성
        - 레코드(로우) → 필드(컬럼)로 구성
        - 필드(컬럼) → 이름, 타입, 속성(PK)로 구성

- 데이터 모델링
데이터를 어떻게 저장할지 정하는 것
    - **Star Schema**
        - 단위 테이블로 나눠 저장하고 필요시 JOIN
        - Production DB에서 사용하는 방식
        - 스토리지 사용량 ⬇️
        JOIN 필요 → 속도 ⬇️
        업데이트 용이
    - **Denormalized Schema**
        - 단위 테이블로 나누지 않고 하나의 테이블로 저장해 JOIN이 필요 없는 형태
        - DW에서 사용하는 방식
        - 스토리지 사용량 ⬆️
        속도 ⬆️
        업데이트 어려움 → Production DB에서 업데이트 후 복사

## SQL (Structured Query Language)

관계형 데이터베이스를 조작하는 프로그래밍 언어

- SQL을 사용하는 이유?
구조화된 db를 사용하는데 sql만큼 검증되고 쉬운 언어가 없기 때문

- DDL (Data Definition Language)
테이블 스키마를 정의하는 언어
- DML (Data Manipulation Language)
테이블에서 레코드를 읽어오는 언어
테이블에 레코드를 추가·삭제·갱신하는 언어

- 단점
    - 구조화된 데이터를 다루는데 최적화됨
        - 정규표현식을 통해 비구조화된 데이터를 다룰 수 있으나 제약이 심함
        - 많은 RDBMS가 플랫한 구조만 지원 (flat ↔ nested (ex. json))
        BigQuery는 nested structure 지원
        - 비구조화된 데이터를 다루기위해 분산 컴퓨팅 환경(Spark, Hadoop 등) 필요
    - RDBMS마다 SQL 문법이 조금씩 다름

## 데이터 웨어하우스 (DW)

- 회사에 필요한 모든 데이터 저장
- SQL 기반의 관계형 DB
- Production DB와 별도
- OLAP
- AWS Redshift, Google BigQuery, Snowflake 등
    - Redshift → 고정비용
    - BigQuery, Snowflake → 가변비용
- 처리 속도보다 처리 데이터 크기가 중요

- ETL·데이터 파이프라인
    - 외부에 존재하는 데이터를 읽어 DW에 저장하는 코드

- 데이터 인프라
    - DW, ETL, 대용량 분산 처리 시스템