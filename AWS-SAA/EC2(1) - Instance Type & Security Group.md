## EC2
- Elastic Compute Cloud, infra 수준의 클라우드 서비스
- EC2 / EBS / ELB / ASG

**EC2의 생성 시 옵션**
- OS
- 컴퓨팅 성능
- 스토리지 공간 → EBS / EFS + *EC2 instance store
- 네트워크 설정 (nic, public ip)
- 방화벽 → security group
- bootstrap script
- ec2 key pair
    - 리눅스 서버에 ssh 접속할 시, 인증 방법은 id+passwd / key 쌍 2가지 방법이 존재
    - ec2 key pair는 ec2 서버에 **ssh 접속**할 때 사용하는 인증 키
    - 최초 생성 시 1번만 다운로드 가능함
    - key pair를 쓰지 않으려면 ssm session manager를 사용할 수도 있음

## EC2 User Data
- User data script를 사용하면 인스턴스를 알맞게 부트스트랩(초기 실행 설정)을 적용할 수 있음
- 스크립트는 인스턴스가 처음 프로비저닝 될 때 딱 한 번 실행됨
    - 사용 예시) 프로그램 설치 / 업데이트 등
- data script는 root 권한으로 실행됨
- c.f) IAM access key vs ec2 key pair
    - **EC2 Key Pair** : 서버 로그인용, ssh 접속을 위함 (public/private 키 쌍으로 구성)
    - **IAM Access Key** : aws api 호출용, aws cli, sdk에서 사용

---

## EC2 Instance Type
- insatnce family / generation / size
- 고려사항
    - CPU / RAM 사용량
    - 네트워크, EBS 성능 등
    - 비용

**사용 목적에 따른 인스턴스 타입**

- Geneal : CPU, 메모리, 네트워크 등의 컴퓨팅 리소스 간 균형이 잘 맞춰짐
    ex) 웹서버, 코드 저장소 등
    
- Compute Optimized : 높은 수준의 프로세서가 필요한 경우
    ex) 일괄 처리, 미디어 형식 변환, 게임 서버, 머신러닝 등
    
- Memory Optimized : 메모리 사용량이 많은 경우
    ex) 데이터베이스, 실시간 비정형 데이터 처리,
    
- Storage Optimized : 잦은 로컬 스토리지 액세스가 일어나는 경우
    ex) OLTP systems(Online Transaction processing), 인메모리 DB, Data warehousing application 등
    

---

## Security Groups

- ip 주소/ 포트 번호 / 프로토콜을 기준으로 접근 제어
- EC2에 붙는 **ENI 단위로 붙는 AWS 네트워크 Stateful 방화벽**
    - Stateful → 허용된 요청에 대한 응답 트래픽은 자동으로 허용
    - 클라이언트측 요청 패킷의 src port 번호는 랜덤값(임시포트)
    - SG는 해당 임시 포트 값을 기억해서 응답을 허용함 → Stateful
- Inbound Rule / Outbound Rule 확인해서 오고가는 트래픽을 제어함
- Allow List > 허용 규칙만 존재 (c.f Deny List)
- 하나의 ENI에 여러개의 SG가 붙을 수 있음 → **Rule들의 Union**으로 동작
- **SG를 설계할 때에는 재사용성을 항상 고려**할 것

![](Pasted%20image%2020260606132039.png)

# Security Group Referencing

> Source/Destination IP에 다른 SG를 지정
- “SG 기반 접근 제어”
- 클라우드에서는 Auto Scaling / IP 변경 / 동적 인프라가 많으므로, IP 기반 허용보다는 특정 역할의 서버 집합으로 접근 제어를 하는게 좋음
- SG는 ENI에 붙음 > SG를 참조한다는 것은 ENI에 할당된 private ip를 허용한다는 의미
    ex) ALB SG → APP SG → DB SG 형식으로 서비스 흐름에 맞게 접근 제어가 가능
    

(+) ssh 접속 허용을 위한 별도의 SG를 만들어서 따로 붙이는 게 좋음
(+) 접속 관련 디버깅
    time out, not accessible → SG 이슈일 가능성 높음
    connection refused → application 단 이슈
(+) Security Group vs OS 방화벽
    OS 방화벽이 있는데 왜 AWS에서는 Security Group를 제공하는걸까?
    → 클라우드 환경에서 서버 내부 방화벽을 사용하면 관리/확장/보안이 어려움

| 항목    | Security Group | OS 방화벽     |
| ----- | -------------- | ---------- |
| 위치    | AWS 네트워크 레벨    | EC2 내부 OS  |
| 적용 시점 | 서버 도달 전        | 서버 들어온 후   |
| 관리 주체 | AWS            | 서버 관리자     |
| 장애 영향 | OS 죽어도 동작      | OS 문제 시 영향 |
| 중앙 관리 | 쉬움             | 어려움        |

    
(+) Security Group vs NACL
    - SG > stateuful, eni를 대상으로 붙음
    - NACL > stateless, sg를 대상으로 붙음