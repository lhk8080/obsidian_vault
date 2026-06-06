# Route53

# DNS

> 도메인 > IP로 변환
- Hirerachial 규칙 (Root / TLD / authoritative)
- Resolver는 public resolver를 사용할 수도 있고, 직접 Resolver를 구축할 수도 있음
![[Pasted image 20260606133652.png]]


- DNS 용어
    
    - Domain Registerar : Amazone Route53, GoDaddy
    - DNS Records : A, AAAA, CNAME, NS 등
    - Zone File : DNS Records 가 있는 지역 파일 (도메인-IP 연결)
    - Name Server
    - Top Level Domain : .com .us. .ort .net
    - Second Level Domain
- ex. [http://api.www.example.com](http://api.www.example.com).
    
    url : **[http://api.www.example.com](http://api.www.example.com).**
    
    . : root
    
    .com. : TLD
    
    .example.com. : SLD
    
    .www.example.com : sub domain
    
    http://api.www.example.com : FQDN, Fully Qualified Domain Name
    

---

# Route53

- HA, Scailable > **완전 관리형 Authoritative DNS**
- Route 53을 통하여 도메인 등록 / 등록된 리소스 상태 health check도가능
- domain/subdomain + record type + value(ip)
- Routing Policy / TTL(resolver caching 수명)

![[Pasted image 20260606133743.png]]

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
> fkdn

- **Weight**
- **Latency**
- **Failover**
- **Geolocation**
- **Geoproximity**
- **IP-Based**
- **Multi-Value**

### Health Check