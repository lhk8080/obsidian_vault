# Kinesis Data Stream
> **실시간** 대용량 데이터를 수집/처리

- 데이터를 일정기간(24시간~365일)동안 저장하여 재처리 가능(replay)
- 데이터를 스트림에서 삭제하려면, 설정 시간이 지나야 함 (만료)
- 여러 comsumer가 동일한 데이터를 동시에 읽기 Ok
- 구성요소
	- producer : 데이터 발신, KPL
	- stream : 데이터 저장 공간
	- shard : stream을 구성하는 데이터 파티션, 처리량은 shard 개수에 따라 결정 (partition key 존재)
	- comsumer : 데이터를 읽어들이는 시스템, KCL
- 용량 모드
	- Provisioned mode : 샤드 개수를 정함
	- On-demand mode : 트래픽에 따라 자동 확장/축소

# Data Firehose
> 실시간 데이터를 받아서 원하는 저장소에 자동으로 저장
- 
- iot 데이터, cloudwatch log, kinesis data stream 등에서 발생한 데이터를 저장해야 함
- data firehose가 S3, Redshift, Opensearch 등의 저장소로 자동 전송 (datadog, splunk 등과 같은 써드파티 지원 ok)
- 완전 관리형 / buffering / lambda와의 연동 / batch write
- 서버리스, 완전 관리형
- 다양한 데이터 형식 지원

| 항목          | Data Streams | Data Firehose |
| ----------- | ------------ | ------------- |
| 목적          | 실시간 스트리밍 처리  | 데이터 저장소로 전달   |
| Consumer 구현 | 직접 구현        | 불필요           |
| 데이터 재조회     | 가능           | 불가능           |
| 데이터 보관      | 최대 365일      | 없음            |
| 운영 난이도      | 높음           | 낮음            |
| Shard 관리    | 필요           | 불필요           |


### Amazone MQ
>RabbitMQ나 ActiveMQ를 직접 설치·운영하지 않고 AWS에서 관리형으로 사용할 수 있는 메시지 브로커 서비스

- JMS, AMQP, MQTT, STOMP 등의 표준 메시징 프로토콜 지원
- Multi-AZ, 백업, 패치, 모니터링 등을 AWS가 관리하는 완전 관리형 서비스
- 기존 코드 변경을 최소화할 수 있어 레거시 시스템의 클라우드 전환에 적합
- AWS 네이티브 신규 서비스라면 SQS/SNS, 기존 메시지 브로커 환경이라면 Amazon MQ 사용이 적합해