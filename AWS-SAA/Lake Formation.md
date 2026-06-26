> AWS에서 데이터 레이크를 쉽고 안전하게 구축/관리할 수 있도록 지원하는 서비스

- 데이터 수집(Ingest), 정제(Cleanse), 변환(Transform)하여 데이터 레이크에 저장
- 데이터 수집, 이동, 카탈로그 생성 등 복잡한 작업을 자동화
- Out-of-the box source Blueprints 
	- 다양한 데이터 소스(S3, RDS, NoSQL DB)로부터 데이터 레이크로 쉽게 가져올 수 있도록 미리 제공되는 데이터 수집 템플릿
	- 데이터 수집 과정을 최소화
- **Fine-grained Access Control** for your application
	- 데이터에 대한 접근 권한을 DB, 테이블, 컬럼 단위까지 세밀하게 제어하는 기능
	- **데이터 접근을 중앙에서 관리**

![](Pasted%20image%2020260626214219.png)



*Data Lake : 다양한 형태의 원본 데이터를 가공하지 않고, 그대로 저장하여 필요할 때 분석에 활용하는 중앙 데이터 저장소 (Raw Data 저장소)

c.f) Data Lake vs Data Warehouse
- Data Lake : 비정형, 반정형, 정형 데이터 모두 저장, Schema-on-Read
- Data Warehouse : 정형화된 데이터 저장, Schema-on-Write
