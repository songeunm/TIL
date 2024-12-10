# In-Memory DB vs. Disk-Based DB

[Velog](https://velog.io/@semoon/In-Memory-DB-vs.-Disk-Based-DB)<br>
# 📍 인메모리 방식 (In-Memory DB)

- 데이터를 디스크(HDD/SSD) 대신 RAM에 저장하여 **처리 속도를 극대화**하는 방식
- 데이터를 메모리에 직접 저장하므로 디스크 I/O가 거의 없거나 최소화
- Redis(NoSQL), Memcached(NoSQL) 가 대표적

### ✔️ 인메모리 방식의 장점

- 초고속 데이터 처리
    - RAM이 디스크보다 훨씬 빠르기 때문에 읽기·쓰기 작업 속도가 매우 빠름 (마이크로초 단위)
- 낮은 지연 시간
    - 실시간 처리가 필요한 애플리케이션에 적합 (ex. 채팅, 실시간 알림)
- 간단한 구조
    - 구조적으로 간단해 빠르게 접근 가능하도록 최적화됨
- 데이터 구조 지원
    - Redis의 경우, 다양한 데이터 구조를 지원하여 유연성 제공

### ✔️ 인메모리 방식의 단점

- **메모리 용량 제한**
    - 데이터를 전부 메모리에 저장하므로 **서버의 메모리(RAM) 용량이 초과되면 성능 저하 또는 데이터 손실 가능**
    - 메모리가 디스크보다 비용이 높기 때문에 대규모 데이터 저장시 비용 부담 큼
- **데이터 영속성 부족**
    - 메모리에 저장된 데이터는 **전원이 꺼지거나 시스템이 재부팅되면 사라질 수 있음**
    - Redis는 RDS(Snapshot)와 AOF(Append_Only File)를 사용해 디스크에 데이터를 저장 가능
    → 그래도 디스크 기반 DB보다 영속성이 약함
- **데이터 동기화 문제**
    - 여러 노드 간 메모리 데이터를 동기화하려면 추가 작업 필요
- **복잡한 쿼리 처리 부족**
    - 빠른 데이터 액세스와 캐싱에 초점 → 복잡한 SQL 쿼리나 트랜잭션 처리에 부적합

### ✔️ 인메모리 방식 사용 사례

- 캐시
    - 자주 사용하는 데이터를 캐싱하여 응답 시간 단축
    - ex. 웹 서버의 세션 데이터, API 응답 캐싱
- 실시간 애플리케이션
    - 실시간 채팅, 알림 시스템, 게임 순위표 등
- 데이터 분석
    - In-Memory OLAP으로 실시간 분석 처리
    - ex. Redis Streams, Spark의 인메모리 연산
- 메시지 브로커
    - 실시간 메시지 전달
    - ex. Redis Pub/Sub

# 📍 디스크 기반 방식 (Disk-Based DB)

- 데이터를 **디스크(HDD/SSD)**에 저장하여 영속성을 보장하는 DB 시스템
- 애플리케이션 종료, 서버 재부팅 시에도 데이터 유지 (영속성 보장)
- MySQL, PostgreSQL, Oracle DB, SQL Server, MongoDB(NoSQL)등이 대표적

### ✔️ 디스크 기반 방식의 장점

- **데이터 영속성 보장**
    - 서버가 종료되어도 데이터 유지
- **대규모 데이터 저장**
    - 데이터를 디스크에 저장하기 때문에 RAM 용량에 구애받지 않음
- **복잡한 데이터 관계 관리**
    - RDBMS는 데이터 간의 관계를 명확히 정의 및 관리 가능
- **복잡한 쿼리 지원**
    - SQL을 통해 복잡한 쿼리와 데이터 조작, 처리 가능
- **트랜잭션 관리**
    - 데이터 변경 작업 실패시, 이전 상태로 되돌릴 수 있는 트랜잭션 기능 제공
- **인덱스와 최적화**
    - 데이터 검색 속도 향상을 위해 다양한 인덱스 구조(B-Tree, Hash Index 등) 지원
- **멀티유저 지원**
    - 동시 접속을 처리
    - 데이터 충돌 방지를 위해 락(Locking) 및 동시성 제어 기능 제공
- **보안 및 권한 관리**
    - 사용자 인증, 데이터 암호화, 접근 권한 제어 기능 제공

### ✔️ 디스크 기반 방식의 단점

- **느린 성능**
    - 데이터를 디스크에서 읽고 쓰기 때문에 메모리 기반 시스템보다 느림
    - 디스크 I/O가 병목이 될 수 있음
    - 이를 해결하기 위해 캐싱 솔루션과 함께 사용 가능 (ex. Redis, Memcached)
- **복잡한 설정 및 관리**
    - 고성능을 유지하려면 인덱스 최적화, 쿼리 튜닝, 서버 설정 등 필요
- **동시성 문제**
    - 여러 사용자가 동시에 데이터를 수정하려 하면, 락이나 트랜잭션 제어로 인해 성능저하 발생 가능
- **비용**
    - 대규모 데이터를 처리하기 위해 고성능 디스크나 하드웨어 필요할 수 있음

### ✔️ 디스크 기반 DB 사용 사례

- 트랜잭션 처리 시스템(TPS)
    - 데이터 무결성, 영속성이 중요한 경우 (ex. 은행, 결제 시스템)
- 복잡한 데이터 관계 처리
- 데이터 웨어하우스(DW)
    - 대규모 데이터 저장 및 분석
- 다양한 애플리케이션 백엔드