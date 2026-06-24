> 대용량 데이터를 저장/분석하기 위한 **AWS Data Warehouse 서비스**

- PostgreSQL 기반으로 만든 OLAP용 DB
- 컬럼 기반 저장 -> 필요한 컬럼만 읽어서 성능 good
- parallel query engine
- 2가지 모드 지원 - Redshift Cluster를 구성하는 방법
	- provisioned cluster
	- serverless cluster
- Amazone Quicksight / Tableu 등의 비즈니스 인텔리전스 서비스와 통합해서 사용 가능

*Data Warehouse : 여러 시스템의 데이터를 한 곳에 모아 분석하기 위한 저장소
	실무에서 운영 DB에 분석 쿼리를 날리면 서비스 성능이 떨어지므로, 별도의 DW를 둠
OLTP : Oline Transaction Proccesing, 실시간 서비스 운영을 위한 데이터 처리 시스템
OLAP : Oline Analytic Processing, 대량의 데이터를 분석하기 위한 시스템

---
### Redshift Cluster
> 여러 노드를 묶어서 하나의 데이터 웨어하우스처럼 동작하게 만드는 Redshift의 실행 환경

- Redshift는 클러스터 단위로 동작, 여러 노드 -> 병렬 처리(MPP, Massively Parallel Processing)
- 구성요소
	- Leader Node : SQL을 받아 분배 / 결과를 취합
	- Compute Node : 쿼리 수행 / 리더에게 전

![](Pasted%20image%2020260624230748.png)
	
(+) Node Slice : Computer Node 내부도 여러 개의 Slice로 나누어서 병렬성을 극대화함


### Redshift DR

- Redshift의 DR은 Snapshot 기반으로 구성, snapshot을 기반으로 새로운 클러스터를 구성
- 내부적으로 스냅샷은 S3에 저장 (증분)
- Snapshot 종류
	- Automated Snapshot : 스냅샷 자동 생성, 설정된 Retetion 기간 동안 보관 
	- Manual Snapshot : 사용자가 직접 생성, 사용자가 삭제할 때까지 유지 (장기보관)

(+) Redshift Mult-AZ
	장애 발생 시 다른 AZ로 자동 전환 하여 가용성을 높임
	컴퓨팅 리소스를 여러 AZ에 배치하여 장애에 대응

### Redshift 데이터 적재 방법

1. Amazone Kinesis Data Firehose
2. S3 using COPY Command
3. EC2 Instance JDBC driver

### Redshift Spectrum
> S3에 적재된 데이터를 Redshift 내부로 적재하지 않고 직접 조회

- 대용량 데이터를 Redshift 내부에 적재하는 것은 많은 비용/시간이 소모
- Redshift Spectrum을 사용하여 S3 데이터를 직접 조회
- but Redshift 내부 데이터보다 성능이 떨어질 수 있음

![](Pasted%20image%2020260624232255.png)