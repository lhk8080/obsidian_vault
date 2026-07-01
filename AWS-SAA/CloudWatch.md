> AWS 리소스 / 애플리케이션의 모니터링, 로그 수집, 알람, 이벤트 자동화를 담당하는 서비스

- Namespace : 수집된 메트릭을 논리적으로 구분하는 최상위 카테고치
- Dimension : 같은 메트릭을 어떤 리소스의 값인지 구분하기 위한 속성 (key-value 형태)
	- 메트릭 별로 30개까지의 디멘션을 가질수 있음
	- ex. instance-id / environment
- `Custom Metric` 지원
- 여러 메트릭을 시각화 하기 위해 `Dashboard`를 구성할 수 있음
- `live tail`을 통하여 로그를 조회하지 않고 실시간으로 확인 가능

### CloudWatch Metric Streams
> CloudWatch에 저장되는 메트릭을 거의 실시간으로 외부 서비스나 저장소에 지속적으로 전송하는 기능

- 외부 시스템이 CloudWatch에서 메트릭을 가져가려면 API 호출을 해야 함 (Pull 방식)
	- API 호출 비용 / 지연 / API Rate Limit 등의 문제가 발생함
- Metric Streams를 사용하면 CloudWatch가 메트릭을 자동으로 외부로 전송함 (Push 방식)
	- Metric Stream은 목적지로 직접 보내지 않고, 보통 Kinesis Data Firehose를 통해 전달함

![](Pasted%20image%2020260701222221.png)

### CloudWatch Logs
> 애플리케이션과 AWS 서비스에서 생성되는 로그를 중앙에서 수집, 저장, 검색, 분석하는 서비스

- 로그 > 각 서버에 접속해서 확인하면 비효율적
- CloudWatch Logs를 사용하면 서버의 로그를 모아 한 곳에서 관리할 수 있음
- 보존 기간 / 접근 권한 / 암호화 등을 설정 가능
- 구성요소 
	- Log Groups : 로그를 관리하는 최상위 단위
	- Log Stream : 로그 그룹 내부의 개별 로그 스트림 (순서대로 저장되는 로그)
- Cloudwatch Log Insight : 로그를 SQL처럼 검색할 수 있음
- S3 Export : CloudWatch Logs에 저장되어 있는 기존 로그를 S3에 내보냄
- CloudWatch Log Subscription : 새로 들어오는 로그를 실시간으로 다른 서비스에 전달
- CloudWatch Log Metric Filter : 로그에서 패턴을 찾아 메트릭으로 변환해줌

### CloudWatch Agent
>EC2나 온프레미스 서버에 설치하여 OS 수준의 메트릭과 로그를 CloudWatch로 전송하는 에이전트 프로그램

- CloudWatch는 일부 메트릭을 CloudWatch에 제공하지만, **OS 내부 정보는 수집하지 못함**
	- OS 내부정보 -> CPU / Disk / RAM / Netstat / Processes / Swap Spaces
- OS 내부 정보를 수집하려면 서버에 CloudWatch Agent를 설치해줘야 함
- Agent가 CloudWatch에 데이터를 보낼 수 있도록 IAM Role 권한을 부여해줘야 함

**Unified CloudWatch Agent**
- 메트릭 + 로그를 하나의 에이전트로 수집하는 최신 CloudWatch Agent
- 기존에는 로그용 / 메트릭 용이 나눠져 있었음
- SSM Parameter Store를 사용하여 설정값 중앙화 가능

### CloudWatch Alarms
> CloudWatch 메트릭 > 임계값을 만족하면 알림을 보내거나 자동 작업을 수행하는 기능

- 알람 상태 (3가지)
	- `OK` : 정상 상태
	- `ALARM` : 조건을 만족한 상태
	- `INSUFFICIENT_DATA` : 판단할 데이터가 부족한 상태
- Period (집계 단위)
	- 메트릭을 하나의 데이터 포인트로 집계하는 시간 간격
	- 사용률은 계속 변화하므로, Period 동안 하나의 값으로 집계함

**Composite Alarms** : 여러 개의 알람을 논리식으로 조합하여 새로운 알람을 만드는 기능

### CloudWatch Network Synthetic Monitor
> AWS 네트워크 구간의 성능과 품질을 지속적으로 모니터링하는 서비스

- 통신이 느려졌을 때 > 어플리케이션 or 네트워크 문제인지 바로 파악하기 여려움
- Network Synthetic Monitor는 네트워크 자체의 상태를 측정해줌
- 측정 항목
	- Latency (지연시간)
	- Packet Loss (패킷 손실 비율)
	- Jitter (지연 시간 변동 정도)
	- Network Availablity (네트워크 연결 확인)