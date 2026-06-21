# API Gateway
> API를 생성/배포/관리하고, 백엔드 서버와 연결해주는  완전 관리형 서비스

- 클라이언트/백엔드 간 API 진입점 역할
- 인증/인가(Authentication & Authorization)
- Throttling : 초당 요청 수(RPS) 제한 및 트래픽 제어
- Caching : 응답 결과 캐싱으로 성능 향상 및 백엔드 부하 감소
- WebSocket 지원 : 실시간 양방향 통신 지원
- API Versioning / Stage 관리 : `dev`, `test`, `prod` 환경 분리
- Swagger(OpenAPI) Import : API 명세서를 가져와 API 자동 생성

```
[Client] -REST API- [API Gateway] -Proxy Request- [lambda]
```


(+) API를 생성(배포)한다. = API를 인터넷에서 접근 가능한 주소(url)로 공개한다.
(+) API Gateway를 통해 AWS 서비스를 외부에 노출할 수 있다

### API Gateway Endpoint Type
> API 공개 범위를 설정하는 옵션

- Edge-Optimized (default) : 글로벌 서비스, Edge에서 요청 처리
- Regional : 특정 리전 내부에서만 사용하는 엔드포인트, 같은 리전 사용자에게 적합
- Private : VPC 내부에서만 접근 가능한 엔드포인트, VPC Endpoint를 통해서 접근

### API Gateway Security
- 인증 - 누가 API를 호출할 수 있는가? (API 호출 자격 검증)
	- IAM Role : IAM 권한으로 API 접근 제어
	- Cognito : AWS Cognito 기반 JWT 토큰 기반 인증
	- Custom Authorizer : 직접 인증 로직 구현