> Apache Cassandra와 호환 > AWS 관리형 NoSQL DB 서비스

- 카산드라를 완전 관리형으로 AWS에서 사용
- serverless / scalable / HA / Multi-AZ
- 설정 옵션 : 
	- on-demand mode
	- provisioned mode


### Apache Cassandra
> 여러 서버에 데이터를 **분산 저장**하는 NoSQL DB> 높은 가용성 + 처리량 확보

- 데이터를 여러 노드에 분산 저장하여 수평 확장에 좋음
- SPOF가 없음, 높은 가용성 확보
- 읽기/쓰기 처리량이 높아 대규모 서비스에 적합
- 스키마 존재, but RDB처럼 엄격하지 않음
- Cassandra Query Language (CQL)

#### Apache Cassandra 기본 구조
```
key space -> Table -> Partition Key -> Colum
```