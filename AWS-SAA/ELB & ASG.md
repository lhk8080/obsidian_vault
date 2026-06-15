# Scalability / High Availability

- Scalability : 확장성, 시스템 혹은 어플리케이션이 더 많은 부하를 감당 가능
    - Vertical : 수직 확장성, 인스턴스의 사양을 올림 - DB와 같은 비분산형 시스템에 적합(RDS, ElastiCache)
    - Horizontal : 수평 확장성 (Elastic), 인스턴스/시스템의 수를 늘림 > 분산시스템
- High Availability : **최소 2개 이상의 AZ**에서 시스템/어플리케이션이 실행되고 있을 때

---

# Elastic Load Balancer

- 외부의 트래픽을 서버에 전달 / 하나의 연결 종단점
- 헬스체크로 인스턴스 상태 파악
- SSL termination / HA / 공개-비공개 트래픽 분리

### ELB Type (4가지)

- CLB
    - 구형, Classic Load Balancer, 레거시용
    - 사용 X
- GWLB : Gateway L B
    - 전송~네트워크 계층
    - 네트워크 장비를 로드밸런싱 (방화벽, IDS, IPS, 트래픽 분석 등)
- ALB : Application LB
    - 어플리케이션 레이어에서 동작
    - `HTTP/2`/ `WebSocket`/`redirect` 지원
    - 경로 기반 target으로 라우팅
    - Target Group > EC2 인스턴스, ECS task, Lambda functions, private iPs
- NLB : Network LB
    - 전송 계층에서 동작
    - TCP / UDP / TLS 지원
    - 고성능, 낮은 지연시간

(+) ALB + Security → Security Group 참조를 통해 LB에 의한 트래픽만 허용하는 것이 Good

(+) Listener Rule : ALB는 Listener Rule을 설정하면 Rule에 맞게 라우팅해줌

# Application Load Balancer

- Listener : ALB가 요청을 받는 포트 (ex. 80, 443)
- Listener Rule : 요청을 어디로 보낼지 결정 (라우팅, forward)
    - 모든 Listener는 Defulat Rule이 존재 > 아무 조건 매칭이 안되면 Default Target Group으로 라우팅
    - API Path / Host / HTTP Header / Query String 기반 라우팅
    - Rule Priority / Forward Action / Fixed Respose / Redirect Action
- Targets : 실제 애플리케이션이 실행되는 대상 (IP, EC2 Instance, Lambda 등이 대상)
- Target Group : 서버들의 논리적 그룹, Rule은 반드시 Target Group을 대상으로 함
- Health Check : **Target Group**이 health check를 위한 요청을 주기적을 보냄 (GET /health)

→ ALB는 여러 Listner를 가질 수 있고, Listener들은 여러 Rule을 가질 수 있음
→ 전체 흐름 : Client > Listner > Rule > Target Group > Target
→ **ALB는 고정 IP가 존재하지 않음**

# Network Load Balancer (v2)

- 전송 계층(L4)에서 TCP/UDP 트래픽을 서버로 포워딩
- 초당 수백 만 건의 요청 처리, 짧은 지연시간
- **AZ 당 하나의 static IP**를 가질 수 있음 + **EIP 할당** 가능
- Target Group으로는 EC2 인스턴스, IP 주소(Private IP여야 함), ALB
- **Health Check는 TCP, HTTP, HTTPS 프로토콜 지원**
- 왜 ALB 앞단에 NLB를 둘까?
    - ALB는 고정 IP를 제공하지 않음 > NLB를 두어 고정 IP 확보
    - AWS *PrivateLink는 NLB만 지원
    
    *PrivateLink : 인터넷을 거치지 않고 다른 VPC의 서비스를 사설망으로 연결
    SaaS 업체에서 제공하는 서비스 연결할 경우 (Snowflake, Datadog)
    

# Gateway Load Balancer

- 보안 장비 로드밸런서 - 네트워크 장비 트래픽을 분산
- 네트워크 계층(L3)에서 동작
- 방화벽, 침입 방지 및 보호 시스템, 패킷 검사 시스템 등
- GENEVE 프로토콜, port 6081

![](Pasted%20image%2020260606131337.png)
## Sticky Session

- Session Affinity / Stickiness
- 로드 밸런서는 요청을 여러 서버로 분산해줌
- but 같은 사용자는 같은 서버로 보내야 할 필요가 있음 (세션 유지) > LB의 세션 기능 사용
- ALB 쿠키를 통해 사용자를 식별, 쿠키가 만료되면 다른 서버로 리다이렉트 될 수 있음
- 스티키 세션을 사용할 때 쿠키의 종류는 2가지
    - Application-based Cookies : 어플리케이션이 직접 생성한 쿠키
        - Custom Cookie
            어플리케이션에 의해 생성 / 쿠키명은 Target Group을 식별할 수 있어야 함
            쿠키명 > `AWSALB`, `AWSALBAPP`, `AWSALBTG` 은 사용X
        - Application Cookie
            LB에 의해 생성, 쿠키명어느 AWSALBAPP
    - Duration-based Cookies : LB에 의해 생성된 쿠키,
        
- TG > Edit attributes > Target selection configuration > Stikiness 설정

(+) 현대의 MSA 환경에서는 세션 말고 Redis/JWT를 사용하기 때문에 ALB Sticky Session을 잘 사용하지 않음

## Cross-Zone Load Balancing

> 로드밸런서가 AZ 구분 없이 모든 Target에 트래픽을 분산해주는 기능

- 가용영역별로 EC2 인스턴스 개수에 차이가 있는 경우 많이 사용함
- LB Node는 AZ 별로 한 대씩 존재 > 알맞게 Target Group에 트래픽 분산을 해줌
- Cross Zone을 활성화 하면 가용영역을 가리지 않고 부하를 고르게 분산
- ALB는 항상 Cross Zone이 항상 활성화됨 (고정 ON)
- GWLB / NLB는 Cross Zone 옵션을 선택할 수 있음 (기본 OFF)

![](Pasted%20image%2020260606131422.png)
cross-zone 옵션이 false인 경우
![](Pasted%20image%2020260606192954.png)
cross-zone 옵션이 true인 경우

- target group과 az
    - Target Group은 AZ 개념이 별도로 존재하지 X
    - LB 생성 시 네트워크 매핑 설정에서 가용영역을 선택할 수 있음, LB는 선택한 가용영역을 대상으로만 트래픽을 라우팅함
- LB와 Reverse Proxy
    [Client] - [LB] - [Server]
    
    - Client 입장에서는 LB만 보이고, LB가 클라이언트 대신 Server와 통신해주는 형태 → LB가 Reverse Proxy 역할을 해줌
    - but 해당 구조에서, Server는 Client의 IP를 알 수 없음 > 서버가 Client ip를 알 수 있도록 LB가 HTTP 헤더를 추가해줌 (`X-Forwarded-For`)
- X-Forwarded Header
    
    프록시 / LB를 거친 원래 요청 정보를 백엔드 서버에 전달하기 위한 HTTP 헤더
    
    - X-Forwarded-Proto : 사용자가 접근한 프로토콜
    - X-Forwarded-Port : 사용자가 접속한 포트
    - X-Forwarded-For : 사용자의 IP 주소
    
    백엔드는 Host Header와 함께 X-Forwarded를 같이 봄
    
    ```python
    GET /login HTTP/1.1
    Host: www.example.com
    X-Forwarded-For: 203.0.113.10
    X-Forwarded-Proto: https
    X-Forwarded-Port: 443
    ```
    

### LB와 SSL

> LB 단에서 TLS를 처리(SSL Termination) 진행

- [Client] -HTTPS- [LB] -HTTP- [Server]
- TLS Offloading > TLS를 사용할 경우, CPU 사용량이 올라감, 중앙에서 TLS 처리를 하면 서버 부하를 줄일 수 있음
- SSL Redirect
- NLB > TLS Passthroughl 가능, LB가 TLS 건드리지 않고 그냥 서버로 전달해줌

### SSL / TLS

> 통신 암호화 프로토콜

- TLS는 SSL의 업그레이드 버전, but 실무에서는 둘이 걍 혼용함
- 기밀성 (암호화) / 무결성(데이터 위변조 방지) / 인증 제공(접속한 서버가 진짜인지 확인)
- TLS Handshake (암호 알고리즘 / 인증서 확인 / 세션 키 - 대칭키)
- TLS는 서버에 대한 인증서를 발급받음 (인증서 > 도메인, 공개키, 발급기관, 유효기간 정보가 존재함)
- CA(Certificate Authority)
    - 브라우저는 CA를 신뢰 > CA가 서명한 인증서는 믿음
- HTTPS Listner
    - SNI(Server Name Indication)
- ACM
    - AWS Certificate Manager

### Connection Draining

> 이미 연결된 사용자의 요청은 처리하고, 새로운 요청은 받지 않는다.

- ALB > Target Group 연결되어 있는데, TG 중 하나의 EC2를 제거해야 할 경우 > 사용자 요청이 강제 종료될 수 있음
- 이를 방지하기 위해 따라서 Connection Draining이 필요
- ALB는 새 요청을 보내지 않고, 기존 연결을 유지하면서 EC2의 사용자 요청 처리 완료까지 기다림
- Target Group > Deregistration Delay 설정 (Target Group에서 Target 제거 후, 연결을 유지하는 시간, 기본값은 300초)
- Connection Draining - CLB / Deregistration Delay - ALB, NLB

---

# Auto Scaling Group

> 변화하는 수요에 따라 인스턴스를 스케일-인/아웃 을 진행

- ASG 생성 > 생성할 인스턴스 MAX/MIN 매개변수 설정
- 인스턴스 상태가 unhealthy 하다면 EC2를 종료하고 새로운 EC2를 띄워서 대체함 (ASG가 인스턴스를 종료)
- Launch Template를 만들어서 ASG를 구성
- ASG는 LB와 통합되어 사용되는 경우가 많음
- ASG는 **CloudWatch 경보**를 기반으로 스케일링 됨
    - 평균 CPU 사용량 / 사용자 지정 메트릭
    - **scale-out / scale-in 정책**을 만들어서 적용

### Scaling Policy

- Dynamic Scaling : 실시간 메트릭을 보고 자동 확장/축소
    - **Target Traking Scaling** : 메트릭 목표값 유지 (AWS 권장)
    - Simple Scaling : 단일 Alarm에 의해 스케일링
    - step Scaling : 구간별 Alarm > 확장량 차등 적용
- Scheduled Scaling : 시간에 맞춰 스케일링 > 정해진 패턴, 예측이 가능할 때
- Predictive Scaling : 과거 데이터를 기반으로 미래 트래픽을 예측

→ Policy는 스케일링 기준이 되는 메트릭 값이 중요 : CPU 사용량, RequestCountPerTarget, Network I/O + Custom Metric


(+) Scaling Cooldown

- 스케일링이 이루어지면, cooldown period에 들어감
- ASG가 cooldown 상태이면 인스턴스를 실행/종료하지 않음 (기본값 300초)