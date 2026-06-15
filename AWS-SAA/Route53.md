
# DNS

> 도메인 > IP로 변환
- Hirerachial 규칙 (Root / TLD / authoritative)
- Resolver는 public resolver를 사용할 수도 있고, 직접 Resolver를 구축할 수도 있음
![](Pasted%20image%2020260606133652.png)


- DNS 용어
    - Domain Registerar : Amazone Route53, GoDaddy
    - DNS Records : A, AAAA, CNAME, NS 등
    - Zone File : DNS Records 가 있는 지역 파일 (도메인-IP 연결)
    - Name Server
    - Top Level Domain : .com .us. .ort .net
    - Second Level Domain
- ex. [http://api.www.example.com](http://api.www.example.com).
    url : **[http://api.www.example.com](http://api.www.example.com).**
    `.` : root
    `.com.` : TLD
    `.example.com.` : SLD
    `.www.example.com` : sub domain
    `http://api.www.example.com` : FQDN, Fully Qualified Domain Name

---

# Route53

- HA, Scailable > **완전 관리형 Authoritative DNS**
- Route 53을 통하여 도메인 등록 / 등록된 리소스 상태 health check도가능
- domain/subdomain + record type + value(ip)
- Routing Policy / TTL(resolver caching 수명)
![](Pasted%20image%2020260606133743.png)

### Record Type
- A : hostname - ipv4
- AAAA : hostname - ipv6
- CNAME : hostname - 다른 hostname
- NS : *호스트 존의 네임 서버

(+) CAA / DS / MX / MAPTR / PTR / SOA / TXT / SPF / SRV
*hosted zone : DNS 레코드를 관리하는 논리적인 공간/서비스
*name server : 실제 작동하는 서버, 도메인의 대한 IP 주소가 어디 있는지 답해주는 시스템

- 작동 흐름
    - 사용자가 브라우저에 example.com을 입력합니다.
    - 전 세계 DNS 시스템은 도메인 구매처에 등록된 **NS 레코드**를 보고, "아, 이 도메인은 AWS **네임서버**에 물어봐야 하는구나!" 하고 찾아갑니다.
    - AWS **네임서버**는 자기 안에 있는 [example.com](http://example.com) **호스티드 존**을 열어서 실제 IP 주소(예: 1.2.3.4)를 확인한 뒤 사용자에게 알려줍니다.
- CNAME vs Alias
    → Load Balancer, Cloudfront 등은 호스트네임(엔드포인트)로 접근 가능
    - CNAME : 호스트네임 > 다른 호스트 네임을 가리키는 레코드
        - 루트도메인 사용 X (*Zone Apex)
    - Alias Record : **AWS 리소스를 가리키는 레코드** (type은 A/AAAA)
        - 루트 도메인 / 루트 도메인이 아닌 경우도 모두 ok
        - ELB / Cloudfront / API Gateway / Elastic Beanstalk / S3 / VPC Interface Endpoints / Global accelerator를 도메인으로 접근할 때
        - 같은 존에 존재하는 route53 route53 레코드도 라우팅 ok
        
        - 요금 부과 X
    
    *zone apex : 도메인의 가장 최상위 루트(서브도메인이 붙지 않은 것, 순수도메인)
    dns 표준 상 CNAME 레코드를 가질 수 없음
    

### Hosted Zones
- 라우팅을 위한 레코드를 가지고 있는 컨테이너
- Public Hosted Zone : 인터넷에서 조회 가능한 DNS 영역
- Private Hosted Zone : 특정 VPC 내부에서만 조회 가능한 DNS 영역
- 달마다 50센트의 비용 청구
- 호스티드 존을 만들면 NS, SOA 레코드가 기본으로 존재
    - NS : Authoritative DNS의 권한은 AWS Route53에게 있다는 걸 명시
    - SOA : Start Of Authority, zone의 기본 정보

### TTL
- Resolver의 캐시 > 질의를 하지 않고 캐시로 바로 응답
- TTL 값이 짧으면 그만큼 트래픽이 많이 발생하므로, 비용 증가

### Routing Policy
> Route53은 **DNS 요청이 들어 올 때 어떻게 응답할 지 결정**할 수 있는 Routing Policy를 지원한다.

- Simple : DNS 조회 시 항상 동일한 응답을 반환 
	- single value > 그대로 사용, **health check와 연동 X**
	- multiple value > 클라이언트에 의해 값이 선택, 라운드 로빈, **health check와 연동 O**
- Weight : 트래픽 비율을 조절
	- DNS 응답 중 일정 비율을 특정 리소스로 리디렉션
	- A/B테스트, 카나리 배포 등에서 사용
- Latency : 가장 응답시간이 빠른 리전
	- AWS가 측정한 가장 응답시간이 빠른 리전으로 연결 (네트워크 경로)
- Failover : 고가용성 구성을 위한 설정
	- 라우트53이 응답 서버에 대한 헬스체크를 진행
	- 헬스체크가 실패하면 다른 서버 쪽으로 리디렉션 (Failover)
- Geolocation : 사용자 국가 기준으로 서비스 연결
	- 콘텐츠 지역화 등에 사용됨
	- 위치에 대한 정보가 없을 때 응답해줄 default 값 설정
- Geoproximity : 사용자-서버의 물리적 거리 기반
	- 기본적으로 가까운 값으로 응답
	- but **Bias 값**을 줘서 더 넓은 영역을 커버할 수 있도록 함 > 서버 트래픽 분산 조정
- IP-Based : 직접 IP 대역을 줘서 라우팅
	- client쪽 CIDR 파악 > 특정 ISP별로 리소스 연결해야 할 때
	- 성능 최적화, 네트워크 비용 감소

*multi value : 로드 밸런싱 X, DNS 필터링(살아있는 IP만 주겠다)

### Health Check
- route53의 health check는 **public endpoint**에 대해서만 동작
	- private 리소스에 대한 health check는 cloudwatch과 cloudwatch alarm을 연계하여 alarm을 health check 대상으로 둘 수 있음
- 여러개의 health check를 조합해서 새로운 helth check를 만들 수 있음 (calculated health check)

### health check process
- AWS는 여러 리전에 Health Checker를 운영 > 대상 서버를 검사
- /heath 경로로 검사 대상에게 요청을 보내고 2XX 응답 혹은 정의한 응답(3XX)을 받으면 정상으로 간주
- https / http / tcp 프로토콜 지원
- health checker가 여러개이므로, checker들의 결과를 종합해셔 상태를 결정(1개가 unhealth여도 바로 최종 상태가 unhealthy X)
- health checker의 요청을 받을 수 있도록 firewall/router등을 설정해야 함

### Calculated health check
- 여러 개의 헬스체크를 논리적으로 하나로 묶음
- or, and, not 연산 가능
- 최대 256개의 child health check


# Hybrid DNS
> 온프레미스 DNS + 클라우드 DNS를 연결해서 하나의 DNS로 통합해여 사용

- Resolver가 VPC 안/밖의 DNS 질의를 다른 DNS 시스템으로 전달
- Route53 - Resolver 메뉴에 들어가서 Inbound Endpoint / Outbound Endpoint / Resolver Rule 생성
- Inbound Endpoint : AWS로 들어오는 DNS
- Outbound Endpoint : AWS에서 밖으로 나가는 DNS

 ![](Pasted%20image%2020260606211740.png)