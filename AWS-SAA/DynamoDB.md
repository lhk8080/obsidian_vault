# DynamoDB
> AWS가 제공하는 완전 관리형 NoSQL DB 서비스
- 초고속, 저지연 읽기/쓰기 성능
- 스키마가 고정되어 있지 않아, item 마다 다른 attribute 값을 가질 수 있음
- 리전 종속 서비스
- Table Class - 데이터 접근 패턴에 따라서 선택할 수 있는 테이블 옵션
	- Standard : 기본, 자주 조회되는 서비스에 적합
	- Infrequent Access : 자주 조회되지 않는 데이터에 적합, 스토리지 비용은 저렴하나, 읽기/쓰기 비용은 더 비쌈
- 기본 구조
	- Table : 데이터를 저장하는 공간
	- Item : 데이터 레코드, pk로 식별, 최대 400kb까지 
	- Attributes : item을 구성한느 속성
- DynamoDB Key
	- Partition Key : 데이터를 저장한 파티션을 결정, 단독 사용 시 유일성 충족해야 함
	- Sort Key : 같은 파티션 안에서 데이터를 정렬하는 기준
	-> Partition Key + Sort Key = Composition Key, 같은 파티션 안에서 여러 데이터를 저장
- Read/Write Capacity Mode - DB에 대한 처리량(Throughput)을 설정
	- Provisioned Mode : default값, 예측 가능한 트래픽, 읽기(RCU)/쓰기(WCU) 처리량을 직접 지정
	- On-Demand Mode : 요청량에 따라 AWS가 처리량 조절, 트래픽이 불규칙하거나 예측하기 어려울 때

## DynamoDB Accelerator : DAX
> DynamoDB를 위한 인메모리 캐시 기능

- 다이나모에 읽기 요청이 많을 때 -> 캐싱을 통해 혼잡 해결
- application 단의 코드 수정 별도로 필요하지 않음
- TTL은 기본값 5분, 마이크로초의 레이턴시

## DynamoDB Stream Processing
> 테이블 데이터 변경 이벤트를 실시간으로 감지, 후속 작업을 수행

- item의 insert / modify / remove 이벤트를 스트림으로 제공
- 테이블의 변경 사항을 기록 -> 다른 서비스가 처리하는 구조 (ex. lambda와 연동, 이벤트 기반 아키텍쳐)
- DynamoDB Stream : 보관기간 24시간, consumer 제한
- Kinesis Data Stream : 보관기간 1년, 많은 consumer, 데이터 처리 방법 多

## DynamoDB Global Tables
> **여러 aws 리전**에 DynamoDB 테이블을 복제

- 여러 리전에 동일한 테이블 생성 -> 데이터를 양방향 자동 동기화 (active-active replication)
- 리전 관계 없이 Read/Write 둘 다 가능
- 다수의 리전에 서비스 -> 지연 시간을 줄이고 싶을 때 글로벌 테이블을 사용
- Global Table 활성화 하려면 먼저 DynamoDB Stream을 활성화 시켜야 함


### 그 외 DynamoDB 추가 기능
- *TTL* : item에 TTL 설정 > 시간 지나면 AWS가 삭제
- *Backup for DR* : DynamoDB 데이터를 백업 > 장애 발싱 시 복구할 수 있도록 함
	- Point in time Recovery (PITR) : 지속적으로 변경 사항을 기록, 최대 35일 이내의 특정 시점으로 복구
	- On-demand backup : 원하는 시점에 기록, 장기간 보관
- *Integration with Amazon S3*
	- export to S3 : DynamoDB 테이블 데이터를 S3로 내보냄 > 분석, 보관, 백업 등에 활용 (PITR 활성화 필요)
	- import to S3 : S3에 저장된 데이터를 DynamoDB 테이블로