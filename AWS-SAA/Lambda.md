# Lambda
> 서버 관리가 필요 없는, 특정 **이벤트** 발생 시 코드 실행하는 **서버리스 컴퓨팅 서비스**

- 코드 업로드 > 서버 프로비저닝, OS 패치, 확장 등을 AWS가 알아서 해줌
- 가격 효율성(pay as you go) / 다양한 프로그래밍 언어 지원  / 다양한 AWS 서비스와 통합
- RAM 설정을 통해 컴퓨팅 성능을 조절함
- 람다의 비용은 (실행 시간 * 할당 메모리) 기준으로 계산
- usecase : 
	- 짧은 작업 처리
	- 이벤트 기반 처리
	- 벡엔드 API 구현

(+) RAM 용량을 늘리면 CPU/Network까지도 성능 개선 -> RAM 용량을 늘려서 실행 시간이 줄어들어 비용이 개선되는 경우도 있음
(+) Lambda Container Image
(+) Custom Runtime API

#### Lambda 등록 흐름
1. 함수 생성 (런타임 선택)
2. 코드 작성
3. 트리거 연결 (ex. S3, EventBridge, API Gateway 등)
4. 권한 설정 
5. 배포 및 테스트

#### Lambda Invocation
> 람다 함수는 동기/비동기 방식으로 호출될 수 있음
- 동기 : 기본값, 함수 실행 결과를 기다림
- 비동기 : InvocationType=Event로 설정, 이벤트 전달만 하고 반환
	- 비동기는 오류 발생 시 자동 재시도 가능, DLQ 설정 가능

### Lambda Limit
> Lambda는 AWS가 설정한 제약사항이 존재함

- 동시 실행 : 계정당 1000건 (필요에 따라 AWS에 한도 증가 요청 가능)
- 환경변수 : 4KB까지
- 디스크(/tmp) : 512MB~10GB
- 실행시간 : 최대 15분
- 메모리 할당량 : 128MB~10GB
- Deployment Size (코드가 의존하는 라이브러리) : 
	- 압축의 경우 - 최대 50MB
	- 압축하지 않은 경우 - 최대 250MB


### Lambda Concurrency / Throttling
- lambda concurrency값은 기본 1000으로 설정
- reserved concurrency를 설정하여, concurrency을 조절할 수 있음
- concurrency 값이 넘어서면 > Throttling 발생
	- 동기 호출 : ThrottleError 발생 (429 Too Many Request)
	- 비동기 호출 : retry -> DLQ(Dead Letter Queue)

#### Cold Start
> 람다 함수 오랫동안 실행 되지 않거나, 새로운 실행 환경이 필요할 때 실행 환경 셋팅에 필요한 초기 지연 시간
- 람다 호출 -> 실행 환경 (컨테이너)가 없으면, 실행 환경을 먼저 생성해야 함
- **최초 호출 / 트래픽 급증** 등 **새로운 인스턴스가 필요한 경우** 발생
- Java, .NET 등 런타임과 의존성이 큰 경우, 콜드스타트 영향이 큼

**Cold Start 줄이는 법**
- Provisioned Concurrency를 사용 (실행 환경을 미리 준비)


### Lambda Snapshot

