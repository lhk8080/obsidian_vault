# Public IP / Private IP

### Public IP

- 인터넷 환경(public 망)에서 기기가 식별될 수 있는 IP 주소
- public IP가 있어야 인터넷을 통해 서버에 접속 가능
- 전체 웹 환경에서 유일해야 함
- ip 기반으로 지리적 위치를 추정 가능함
    - IP > ISP/국가/지역 단위로 할당

### Private IP

- private 네트워크 상에서 기기가 식별될 수 있는 IP 주소
- 서로 다른 사설 네트워크끼리 > IP 주소가 겹쳐도 상관 NO
- 개인 컴퓨터가 사설 IP를 할당받아도 > NAT + Internet Gateway(proxy)를 사용하면 인터넷 연결 가능
- 지정된 IP 대역만 사설 IP로 사용 가능
    - Class A : 10.0.0.0/8
    - Class B : 172.16.0.0/12
    - Class C : 192.168.0.0/16

### Elastic IP

- EC2 인스턴스가 중단되고 다시 시작되면 Public IP값이 변경됨
- IP 고정을 하려면 Elastic IP를 따로 할당해야 함
- 인스턴스 장애 상황에서 EIP를 사용하면 failure masking 가능 → EIP만 떼서 다른 인스턴스에 붙이면 되니까
    - but 현재는 ALB, NLB 등을 일반적으로 많이 사용
- 한 계정 당 EIP는 5개까지만 소유 가능 (더 필요하면 따로 AWS에 문의)
- EIP 사용은 최소화 하는 게 좋음

(+) private ip는 인스턴스가 종료되어도 변경되지 않음

c.f) Loopback : 127.0.0.0/8

# Placement Group

> EC2 인스턴스들의 물리적 배치 전략을 제어하는 기능

- EC2 전략 배치를 짜기 > Placement group을 정의
- EC2 → 필요에 따라 Placement Group을 생성 → EC2 생성 中 고급 옵션에서 Placement Group 할당

### Cluster Placement Group
- 성능 최우선(10 Gbps)
- low-latency / single AZ → high performance / high risk
- HPC, 빅데이터 처리 → MPI, 실시간 분석, 초고속 통신

### Spread Placement Group
- 장애 분산 → 장애 격리 / 고가용성
- 매우 중요한 서버를 관리할 때 (동시에 죽으면 안되는 서버)
- AZ당 최대 7개의 인스턴스만 배치 (강한 하드웨어적 제약)

### Partition Placement Group
- 대규모 분산 시스템 안정성
- 인스턴스를 여러 파티션에 나눠서 배치 → 하나의 AZ, 다수의 Rack에 분산 배치
- 파티션 = 랙 묶음 / 서로 다른 파티션은 랙을 공유하지 않음 →AZ 당 7개 이하의 파티션
- HDFS, Hadoop, Cassandra, Kafka