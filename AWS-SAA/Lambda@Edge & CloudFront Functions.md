
# Cloudfront Edge Functions
> 엣지 로케이션에서 요청/응답을 처리
- 사용자와 가까운 Edge Location에서 요청을 처리
- 지연 시간 감소 + 원본 서버 부하 감소
- Cloudfront Functions : 가벼운 요청 처리
- Lambda@Edge : 복잡한 로직 처리
- usecases : 
	- URL Rewrite
	- HTTP -> HTTPS 리다이렉트
	- 국가별 콘텐츠 제공
	- JWT 인증 및 권한 검증
	- A/B테스트 및 사용자별 트래픽 분기

Cloudfront / Origin 서버 간 요청 흐름
![](Pasted%20image%2020260620233715.png)

---
# CloudFront Functions
> CloudFront 엣지 로케이션에서 초경량 요청/응답 로직을 실행
- **JS 런타임**만 지원
- 매우 **짧은 실행 시간**이 필요한 작업에 적합
- **Viewer Request / Response 단계**에서만 실행
- URL 재작성, 헤더 조작, 리다이렉트, A/B 테스트 등에 사용


# Lambda@Edge
> CloudFront의 엣지 로케이션에서 복잡한 비즈니스 로직을 실행하는 Lambda 서비스
- CloudFront 요청/응답 단계에서 Lambda 함수를 실행
- Node.js, Python 런타임 지원
- Viewer Request/Response, Origin Request/Response 단계에서 실행 가능
- JWT 인증, 국가별 콘텐츠 제공, 이미지 처리, 외부 API 연동 등에 사용
- CloudFront Functions보다 기능은 강력하지만 실행 속도와 비용 부담이 더 큼



https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/edge-functions-choosing.html
https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/lambda-at-the-edge.html
