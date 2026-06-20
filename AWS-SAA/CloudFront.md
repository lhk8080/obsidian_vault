## CloudFront
> 전 세계 엣지 서버 > 콘텐츠 캐싱 > 응답 속도 개선
- CDN 서비스, Content Delivery Network
- DDoS 공격에 대해서 보호
- Origin - 실제 데이터를 가져오는 대상 원본 서버, 캐시에 없으면 오리진 접근
	- S3 bucket : OAC(Origin Access Control)
	- VPC Origin : Private ALB, NLB, EC2 Instance (VPC 내부의 Private 리소스)
	- Custom Origin : S3 Website, public HTTP backend, API Gateway, on-premise server
- Distribution - Cloudfront 배포 단위, 하나의 Distribution에 여러 Origin, path pattern

(+) Cloudfront vs S3 Cross Region Replication

#### Geo Restriction
> 특정 국가 단위로 Cloudfront 콘텐츠 접근을 제한
- Allowlist / Blocklist 를 설정하여 접근을 제한할 수 있음
- src ip에서 지리적 정보를 얻어 지리적으로 제한을 함(국가 단위)
- 저작권, 법적 규제, 비용 절감 등
#### Cache Invalidation
- origin이 업데이트 되었을 때, cloudfront는 알지 못함
- TTL이 만료 되었을 때에만 업데이트 사항이 적용됨
- 강제로 refresh 하려면 cache invalidation을 진행
- 경로별로 캐시 무효화 가능

### AWS Global Accelerator

- 서버 배포 > 글로벌 사용자가 접근하려면 많은 홉을 거쳐야 하므로 레이턴시가 길어짐

- 인터넷 구간 최소화, AWS 전용 네트워크를 사용하여 지연시간 + 가용성 개선
- Anycast IP를 사용, 가장 가까운 Edge 서버가 트래픽을 받아서 AWS 백본망을 통해 최적의 엔드포인트로 전달
- 2개의 고정 anycast public IP를 제공
- health check / security / HA



(+) cloudfront vs global accelerator


[Lambda@Edge & CloudFront Functions](Lambda@Edge%20&%20CloudFront%20Functions.md)