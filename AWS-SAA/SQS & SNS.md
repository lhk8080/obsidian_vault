
## Application Integration
- 다수의 어플리케이션 간 통신이 필요한 경우(**MSA**), 구현 방법은 2가지 (동기식/비동기식)
- 두 앱이 동기식으로 직접 연결될 경우, 특정 서비스의 장애 및 지연이 다른 서비스로 전파될 수 있음
- 이를 해결하기 위해 **느슨한 결합**을 구현함 (비동기 통신 > 서비스 간의 의존성을 낮춤, decoupling)
	- SQS (queue)
	- SNS (pub/sub)
	- Kinesis (real-time streaming)
## SQS
> 작업을 쌓아두고 하나씩 처리
- 완전 관리형 서비스 
- 무한한 처리량(쓰루풋), 무한한 길이의 큐, 빠른 지연시간, 
- 메시지의 저장 기간은 4~14일 까지
- 메시지 길이는 1024kb 미만
- 구성요소
	- SQS queue : 메시지 적재하는 큐
	- producer : 메시지 생산자, 하나의 큐에 다수의 생산자가 존재할 수 있음, 코드 내에서 SDK를 통해 메시지를 발행/전송(SendMessage API)
	- comsumer : 메시지를 polling, 메시지를 처리한 후 SQS에게 DeleteMessage API를 보냄
- SQS 종류는 2가지 
	- Standard Queue : 높은 처리량 우선
	- FIFO Queue : 메시지 순서 보장

(+) polling : 충돌 회피 / 동기화 처리를 위해 상태 확인을 일정 주기로 함
	c.f) polling vs interrupt, event-driven

### SQS Standard Queue
- 높은 처리량을 위해 분산 저장을 사용함 -> **병렬 처리 때문에 빠르지만, 순서 보장은 못함**
- 중복 전달 가능, At-least-once delivery, 메시지 순서 보장 X, Best Effort ordering

### SQS FIFO Queue
- First In First Out / Standard Queue와 다르게, 큐의 순서를 보장함
- 같은 메시지가 여러 번 전송되는 것을 방지하기 위하여 **중복 제거 기능**을 제공 (Duplication ID 사용)
- MessageGroupID를 통해 그룹별 순서를 보장

|Standard|FIFO|
|---|---|
|High Throughput|Ordering|
|Best-effort ordering|Guaranteed ordering|
|At-least-once|Exactly-once|
|Duplicate possible|No duplicate|

#### SQS Security
- HTTPS로 in-flight 암호화 (or clinet-side 암호화 가능)
- IAM Policy로 SQS API 호출에 대한 Access Control
- SQS Access Policy 설정 (S3 Access Policy랑 유사)

#### Message Visibility Timeout
- 메시지가 소비자에 의해 폴링되면, 다른 사용자에게 노출되지 않음
- 타임아웃 기본 시간은 30초 -> 30초 안에 메시지가 처리가 되어야 한다는 의미
- 타임아웃 시간동안 메시지가 처리되지 않으면, 해당 메시지는 다시 SQS에 노출됨
- 메시지 처리에 더 많은 시간이 필요하다면, 소비자 단에서 **ChangeMessageVisibility API**를 호출해서 타임아웃값을 늘림
- **메시지 처리하는 어플리케이션 성능에 맞게 타임아웃값을 설정하는 것이 중요함**

#### Long Polling
- SQS에 메시지가 없더라도, 일정 시간 동안 대기했다가 SQS가 응답
- SQS에 메시지가 들어오면 > 바로 소비자에게 메시지 전송
- 불필요한 API 호출 감소 / 빈 응답 감소 / 메시지 수신 지연 감소 / CPU, Network 자원 절약
- 큐 레벨 / API 호출 레벨에서 LongPolling 시간 설정 가능
- long polling은 1초~20초 사이에서 설정 가능

(+) SQS + ASG
	SQS 큐 길이 기반으로 스케일링을 진행할 수 있음
	SQS > Clould Watch Metric > CloudWatch Alarm > ASG

## SNS
> 이벤트를 여러 시스템에 전파(브로드캐스트)
- Simple Notification Service
- 구성요소
	- Topic : 메시지 발생 채널, Publisher는 Topic에 메시지를 보냄
	- Publisher : 메시지를 보내는 시스템
	- Subscriber : Topic을 구독하는 대상, 
- Message Filtering을 통해 Subscriber 마다 필터 조건을 설정 > 필요한 메시지만 전달할 수 있음
- SQS와 마찬가지로 Standard / FIFO 옵션이 존재

| 항목     | SNS          | SQS              |
| ------ | ------------ | ---------------- |
| 방식     | Push         | Pull             |
| 목적     | 브로드캐스트       | 작업 큐             |
| 전달 방식  | 여러 곳에 동시에 전달 | 하나의 Consumer가 처리 |
| 메시지 보관 | 짧음           | 저장 후 소비          |
| 사용 패턴  | 1:N          | 1:1              |


(+) SQS + SNS : Fan Out 패턴