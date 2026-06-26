> **빅데이터 프레임 워크**를 AWS 환경에서 쉽게 구축하고 실행할 수 있는 관리형 빅데이터 처리 서비스

- 빅데이터 프레임워크를 위한 분산 처리 인프라를 AWS가 대신 구축/운영해줌
- 빅데이터 작업은 Batch 작업이므로, Spot Instance를 사용해서 비용을 줄일 수 있음
-  EMR 지원 프레임워크 : Spark, Hadoop, HBase, Presto 등등

### EMR Cluster 
- Primary Node : 클러스터를 관리 
- Core Node : 데이터 저장 및 처리
- Task Node : 데이터 처리만 수행 (Spot Instace 사용)