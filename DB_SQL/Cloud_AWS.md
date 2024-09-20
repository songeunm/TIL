[Velog](https://velog.io/@semoon/Cloud%EC%99%80-AWS)

## Cloud

컴퓨팅 자원을 네트워크를 통해 서비스 형태로 사용하는 것

- 장점
    - 초기 투자 비용 (CAPEX) ⬇️
    - 운영 비용 (OPEX) ⬆️
    - 리소스 준비 시간 ⬇️
    - 글로벌 확장 용이
    - 소프트웨어 개발 시간 ⬇️ → Managed Service (SaaS) 이용

## AWS

가장 큰 클라우드 컴퓨팅 서비스 업체

- **EC2 (Elastic Compute Cloud)**
    - AWS 서버 호스팅 서비스 (linux, window, mac 지원)
- **S3 (Simple Storage Service)**
    - AWS 대용량 클라우드 스토리지 서비스
    - 계층적 구조 지원
    - 디렉토리 = 버킷

- **RDS (Relational Database Service)**
    - MySQL, PostgreSQL, Aurora 등 Production DB 서비스형태로 지원
- DynamoDB
- **Redshift**
- ElasticCache
- Neptune (Graph database)
- ElasticSearch
- MongoDB

- Lambda
    - serverless computing engine → EC2 론치 필요 X
    - Google - Cloud Function, Azure - Azure Function 도 유사한 기능

## Redshift

Scalable SQL 엔진

- OLAP
- Columnar storage → 컬럼별 압축 가능, 컬럼 추가·삭제 빠름
- BULK UPDATE 지원
    - 데이터를 S3로 복사 후 COPY 커맨드를 사용해 Redshift로 일괄복사 → 속도 빠름
- 고정 용량/비용
- PK uniqueness 보장 X → 개발자가 보장해야함

- PostgreSQL 8.x 와 SQL이 호환됨
    - PostgreSQL 8.x의 모든 기능 지원 X
    - PostgreSQL 8.x를 지원하는 툴· 라이브러리로 액세스 가능 (JDBC, ODBC)
        - SQL Workbench(Mac, Window), Postico(Mac)
        - Python - psycopg2 모듈
        - BI Tool - Looker, Tableau, Power BI, Superset

- Schema 구성
    - DEV
        - raw_data
        외부에서 읽어온 테이블
        - analytics
        분석용 테이블
        - adhoc
        테스트·개발용 테이블