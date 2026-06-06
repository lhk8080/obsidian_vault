# RDS

> AWS의 관계형 DB 서비스

- AWS 관리형 서비스로 > 다양한 DBMS 사용 가능
- Postgres / MySQL / MariaDB / Oracle 등 **(+) Aurora (AWS 독점 DB)**
- 왜 RDS를 사용하는가?
    - 자동화된 프로비저닝, OS 패치
    - 지속적인 백업, 특정 타임스탬프 상태로 복원 (Restore)
    - 모니터링 대시보드 제공 / Read replica /Multi AZ (재해 복구) / 수직-수평 확장
    - 스토리지 자동 확장 (Storage Auto Scaling)
- **RDS 인스턴스는 SSH로 접속 불가**

### Straoge Auto Scaling

- 디스크 용량이 부족하면 스토리지(EBS)를 자동으로 늘려줌 (DB 인스턴스가 늘어나는 것 X)
- 스토리지 축소는 X (데이터 손실 위험)
- RDS 생성 시 > Allocate Storage 할당 / Storage Autoscaling 설정 / Maximum Stroage Threshold(임계값) 할당 : Allocate ~ Maximum 사이에서 자동 증가
- 스토리지 확장 기준 : 3가지 조건이 **모두 충족**되어야 함
    - 할당된 스토리지 용량 중 남은 용량이 10% 미만인 경우
    - low storage 상태가 5분 이상 지속된 경우
    - 마지막 확장 이후 6시간 이상 지난 후

### Read Replica

- “읽기”만 가능 > SELECT 구문만 유효
- 최대 15개의 Read Replica 생성 가능
- 동일 AZ / 다른 AZ (네트워킹 비용 발생X) / 다른 Region
    - Cross AZ : 네트워킹 비용 X / Cross Region : 비용 O
- Replication는 비동기로 진행 > 복제 지연
- Replica > Primary로 승격 가능 (DR, 수동 promote)

### Multi AZ

- DR(Disaster Recovery)를 위한 동기 Replication
- 하나의 엔드포인트로 DB 접근 > 장애 시 대기하고 있던 인스턴스가 마스터로 자동으로 사용됨 > 자동 Failover
- **Sync replcation**이 일어남

### RDS Custom

- Oracle / MS SQL Server를 사용할 때 RDS Custom을 사용할 수 있음 > OS와 DB까지 직접 관리
- OS 및 DB에 직접 접근 가능 > 사용자 정의 (설정값, 패치, 네이티브 기능, ssh/ssm을 통해 ec2 인스턴스 접속)
- but 백업/모니터링/자동화/복구 등의 기능은 RDS 그대로
- DB 인스턴스에 대한 OS 레벨의 접근이 필요할 때
    - ex) OS에 모니터링 에이전트 설치, Oracle Native 기능 활성화, 커널 파라미터 수정
- RDS Custom은 완전 관리형 vs 직접 관리의 중간 어딘가의 서비스를 제공함

### RDS Backups

- Automated backups
    - RDS가 자동으로 백업을 수행, point-in-time recovery(PITR)
    - Daily Snapshot / Transaction Log Backup > 하루에 한 번 스냅샷 생성, 5분마다 로그 백업
    - 1~35일 사이의 보존 기간 > 비활성화 하려면 유지기간을 0으로 설정
- Manual DB Snapshot
    - 사용자가 직접 Snapshot 버튼을 누름
    - 원하는 기간만큼 유지할 수 있음

(+) DB 백업은 비용 절감 기법으로도 사용 > 사용하지 않을 때에는 DB 지우고, 사용할 때 스냅샷으로 복원

- RDS vs RDS Custom
    
    RDS : DB 및 OS 전체를 AWS가 관리
    
    RDS Custom : DB 및 OS에 대해 전체 관리자 권한 제공
    
- Single AZ RDS > Multi AZ RDS 변환 방법
    
    무중단으로 Multi AZ로 바꾸려면?
    
    modify
    
    primary DB가 찍은 스냅샷 > Standby DB로 복구 > Primary&Replica 사이 자동 Sync
    
- DB 스토리지 용량 축소
    
    - 오래된 로그 삭제
    - S3 아카이빙
    - TTL 정책

---

# Aurora

> AWS가 만든 **클라우드 네이티브 관계형 DB**

- MySQL / PostgreSQL과 호환, but MySQL / PostgreSQL 대비 5배 이상의 성능
- 사용량에 따라 자동으로 스토리지 증가(10GB ~ 256TB)
- 15개의 레플리카를 가질 수 있고, 레플리케이션 속도도 빠름(10밀리초)
- HA native, 기본 RDS 보다는 비용 부담, self healing, peer-to-peer replication, backtrack
- 3개의 AZ 중 6개의 데이터 복제본을 가짐(6개 中 4개는 write 연산 가능 / 6개 中 3개는 read 연산 가능)
![[Pasted image 20260606131710.png]]
![[Pasted image 20260606131716.png]]

- 하나의 master - read/write 연산 가능 / 여러 개의 replica - read 연산만 가능
- writer용 endpoint, reader용 endpoint가 따로 존재
- reader endpoint로 DB에 접근하면 replica 간 로드밸런싱을 내부적으로 진행해서 연결됨

### Aurora Backups

- Automated Backups 가능 (유지기간 1~35일, RDS와 다르게 비활성화 X)
- Manual Backup도 OK

### Aurora 고급

1. **Custome Endopoint** : 워크로드에 맞춘 별도의 Endpoint 생성
    
    ex. read replica 인스턴스 사양을 다르게 두어서 분석형 쿼리 전용으로 사용
    
2. **Aurora Serverless** : 예측할 수 없는 워크로드 > 적응형으로 서비스, 사용한 만큼 지불 > 비용 효율적
    
3. **Global Aurora** : Cross Region 서비스, DR 목적
    
    1. **Auraro Cross Region Read Replica**
        - 보통 읽기 분산, 비동기 기반 복제로 인한 지연
        - **DB 엔진 기반 복제**
    2. **Aurora Global Database**
        - Primary / Secondary Region을 등록
        - **Storage 기반 복제**
        - *_cross-region replication 1초 미만 / _RTO 1분 미만__
4. **Aurora Machine Learning** : SQL로 ML서비스를 호출해주는 기능
    
    - 기존 : DB에서 데이터 조회 > ML API 호출 > ML 예측 > 결과 반환
    - Aurora ML 사용 : Aurora > Sagemaker (데이터 추출 없이 바로 DB에서 ML 호출)
    - Amazone SageMaker / Amazone Comprehend 등을 Aurora에서 사용 가능
    - 사기 탐지, 광고 타겟팅, 고객 이탈 예측 등의 분석을 위해 사용
5. Babelfish for Aurora PostgreSQL
    
    - SQL Server와 Postgre SQL은 SQL 문법이 다름 (T-SQL 등의 문법도 PostgreSQL은 지원X)
    - SQL Server > PostgreSQL로 마이그래이선 하려면 많은 작업이 필요, 해당 작업을 중간에서 도와주는 게 Bablefish for Aurora PostgreSQL (마이그레이션 부담 감소 도구)
6. Aurara Database Cloning
    
    - 기존 Aurora DB를 복제해서 새로운 클러스터를 생성 > **같은 볼륨을 공유**
    - 일반적인 복제의 경우, 새로운 스토리지 볼륨이 필요하고 별도의 데이터 복제 필요
    - **Copy-on-Write**, 새로운 클러스터용 스토리지 생성, 변경된 블록만 별도로 저장
    - 빠르고 비용 효율적
    - ex. Production DB를 기반으로 개발 환경 / 테스트 환경을 구축, DB 업그레이드 테스트

![[Pasted image 20260606131731.png]]

*OLTP : transactional query, 일반 쿼리, 데이터 조회용

*OLAP : analytical query, 분석형 쿼리, 수백~수억 건 데이터 스캔 > 집계를 내는 경우, cpu 사용량 높음, aggregation

*RTO : Recovery Time Object, 비즈니스 정상화를 위해 허용되는 최대 복구 시간

---

## RDS / Aurora의 복원

1. 새로운 DB 생성 시 존재하는 스냅샷을 사용
2. S3에서 RDS DB를 복원

### RDS/Aurora Security

> RDS/Aurora는 데이터를 암호화 하여 보호할 수 있음

- at-rest 암호화 : Master/Replica > AWS KMS에 의해 스토리지에 저장되는 데이터들을 암호화
- in-flight 암호화 : 기본값으로 TLS 기반 암호화 설정, Client 단에서는 TLS 루트 인증서가 필요
- IAM Authentication : DB 접속시 필요한 user/pw 대신 IAM Role을 사용하여 접근할 수 있음
- Security Group : SG로 네트워크 접속 제어
- ssh 접속 차단 : RDS Custom이 아니라면 ssh로 접근 X

*at-rest enscrytion : 저장된 데이터 암호화

*in-flight enscrytion : 전송 중인 데이터 암호화

### RDS Proxy

- **Connection 관리 문제를 해결** (여러 서버가 같은 DB 서버에 접근할 경우 > Connection 고갈/관리 문제)
    
    ex. lamdba 1000 개 호출 > DB Connection 1000개
    
- 전역 범위에서 DB Connection Pool을 유지
    
- 최소한의 Connection만 열여서 DB 서버의 컴퓨팅 리소스 소모를 최소화
    
- Serverless > AutoScaling, HA
    
- **연결 관리를 대신하면서 Failover 감지하고, Writer를 연결해서 Connection을 재사용함** (장애조치 시간 감소)
    
- IAM 권한으로 DB에 접근해야 할 때 RDS Proxy 사용
    
- Private SN에서만 존재해야 함, VPC 내에서로 접근
    

---

# Elasticache

> Redis, Memcached와 같은 캐시 기술을 사용할 수 있는 AWS 서비스

- 인메모리 DB > 높은 성능과 적은 지연시간
- DB 서버에 대산 부하를 줄여줌
- stateless한 application
- *캐시 무효화 전략이 반드시 있어야 함
- AWS 관리형 서비스 > OS 패치, 최적화, 셋업, 설정, 모니터링, 장애 복구

### Redis

- **Multi AZ**, **Auto Failover**, **Read Replica**
- *AOF 방법으로 데이터 영속화
- backup / restore 기능
- 다양한 자료구조 지원
- IAM 인증을 통한 Redis 접근
- Redis Auth (Redis 클러스터에 pw/token으로 접근), 데이터 전송 시 SSL 암호화 지원

### Memcached

- 데이터를 **여러 노드에 나눠서 저장** (sharding, partitioning)
- 원래 Memcache는 복구에 약하지만, AWS 관리형 차원에서
- No HA(replication), persistent
- **Multi-threaded 구조** / **백업/복구(**serverless)
- 분산 메모리 캐시 서버, **Key-Value 캐시** (굉장히 단순한 형태, 기능과 구조가 단순해서 오버헤드가 적음)
- SASL 기반 인증 지원

*Cache Invalidation - 원본 데이터 변경 시 캐시에 있는 데이터를 제거하거나 갱신하는 방법

- TTL (데이터 최신성이 떨어짐)

*AOF - Append Only File, Redis 영속화 방식

- 모든 쓰기 명령을 로그 파일에 기록, Redis 재시작 시 명령을 다시 실행해서 데이터 복구
- 복구 기간이 길 수 있음

*Redis Sorted set : 집합 + 점수를 가지고 자동 정렬 (ZSET)

랭킹 / 대기열 순번 등에 사용

### Elasticache 사용 패턴

- Lazy Loading : Cache Aside, 모든 데이터는 캐시를 통해 읽는 패턴
    
    cache miss 시 DB 조회 후 캐시에 데이터 올림
    
- Write Through : 쓰기 시 캐시와 DB를 동시에 갱신 > 메모리 낭비 될 수 있음
    
- Session Store : 세션 데이터를 캐시에 올림 (TTL 설정 필요)
    
- (+) RDS Database Port
    
    PostgreSQL(5432) / Mysql(3306) / Oracle(1521) / MSSQL (1433) / MariaDB(3306) / MySQL(3306) / Aurora(5432)