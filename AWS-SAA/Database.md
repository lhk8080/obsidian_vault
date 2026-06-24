# Database Type

- RDBMS
- NOSQL
- Object Store
- Data Warehouse
- Graph
- Search
- Time series

### RDS

- 관리형 -> PostgreDB / MySQL / Oracle / SQL Server / MariaDB / DB2 + Custom
- EBS 볼륨 + 인스턴스 프로비저닝
- 스토리지는 자동으로 오토스케일링 
- Read Replica / Multi AZ 지원
- 자동 백업 / DB Snapshot

### Aurora
> RDS보다 유지관리는 덜하고, 성능/기능이 향상된 버전

- PostgreSQL / MySQL 엔진에 호환
- HA  > 3개의 AZ에 걸친 6개의 레플리카 (+ self healing, auto-scaling)
- custom endpoint
- **aurora serverless** : 트래픽에 따라 DB 용량을 자동으로 확장/축소 (예측하기 어려운 워크로드)
- **aurora global** : 여러 리전에 데이터를 복제 > 글로벌 서비스/DR 지원
- aurora machine learning
- aurora database cloning

### ElastiCache
> Redis/Memcached > 인메모리 데이터 저장소

- 캐시용, 짧은 응답시간
- Multi AZ / Read Replica 지원
- Backup / Snapshot / Point in time


[RDS & Aurora & Elasticache](RDS%20&%20Aurora%20&%20Elasticache.md)

---
### DynamoDB
> 관리형 서버리스 NoSQL


- ElastiCache를 대체하여 key-value store로 사용 가능
- HA, Multi-AZ, 
- DAX / Event Processing / Global Table

[DynamoDB](DynamoDB.md)

---
### S3
> 객체 형태로 데이터를 저장하는 객체 스토리지 서비스

- Serverless 형태
- Object형(key-value) Data Store
- lifecycle policy 설정
- versioning / encryption / replication / MFA-delete / Access Log 등 지원

[S3](S3.md)

---

![[DocumentDB]]


![[Naptune]]


![[Keyspaces]]


![[Timestream]]



