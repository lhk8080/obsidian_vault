
## Application Integration
- 다수의 어플리케이션 간 통신이 필요한 경우(**MSA**), 구현 방법은 2가지 (동기식/비동기식)
- 두 앱이 동기식으로 직접 연결될 경우, 특정 서비스의 장애 및 지연이 다른 서비스로 전파될 수 있음
- 이를 해결하기 위해 **느슨한 결합**을 구현함 (비동기 통신 > 서비스 간의 의존성을 낮춤, decoupling)
	- SQS (queue)
	- SNS (pub/sub)
	- Kinesis (real-time streaming)

## SQS
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
- 높은 처리량을 위해 분산 저장을 사용함 -> 병렬 처리 때문에 빠르지만, 순서 보장은 못함
- 중복 전달 가능, At-least-once delivery, 메시지 순서 보장 X, Best Effort ordering





### SQS FIFO Queue







|Standard|FIFO|
|---|---|
|High Throughput|Ordering|
|Best-effort ordering|Guaranteed ordering|
|At-least-once|Exactly-once|
|Duplicate possible|No duplicate|