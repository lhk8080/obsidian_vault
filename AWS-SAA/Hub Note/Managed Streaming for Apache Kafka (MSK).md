> apache kafka를 aws에서 서버 관리 없이 사용할 수 있는 완전 관리형 메시지 스트리밍 서비스

- kafka 클러스터의 생성, 운영, 패치, 장애 복구 등을 AWS가 자동으로 관리
- 실시간 스트리밍 데이터 수집 및 전달에 사용
- 높은 처리량 / 낮은 지연시간 / EBS 볼륨에 데이터 저장
- Amazone Kinesis의 대안 (Streamed Data)

### 구성 요소
- Producer : Kafka에 메시지를 전송하는 주체
- Topic : 메시지를 저장하는 논리적 채널
- Partition : Topic을 분할하여 병렬 처리 지원
- Comsumer : Topic에서 메시지를 읽어 처리하는 주체
- Broker : Kafka 서버 

![](Pasted%20image%2020260626224908.png)

c.f) MSK vs Kinesis Data Stream

| MSK                            | Kinesis Data Streams |
| ------------------------------ | -------------------- |
| Apache Kafka 기반                | AWS 자체 스트리밍 서비스      |
| Kafka API 및 생태계 활용             | AWS 서비스와 높은 통합성      |
| Kafka 애플리케이션 이전(Migration)에 적합 | AWS 네이티브 환경에 적합      |
| Kafka 클라이언트 사용                 | AWS SDK 사용           |

c.f) MSK vs SNS

| SNS                  | Kafka (MSK)            |
| -------------------- | ---------------------- |
| 이벤트 알림(Pub/Sub)      | 이벤트 저장 및 스트리밍          |
| 메시지를 즉시 전달           | 메시지를 일정 기간 저장          |
| Consumer가 없어도 전달 후 끝 | Consumer가 나중에 읽을 수 있음  |
| 메시지 재생(Rewind) 불가    | Offset으로 과거 메시지 재처리 가능 |
| Fan-out에 강함          | 대용량 이벤트 파이프라인에 강함      |

