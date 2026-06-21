# Cognito
> 웹/모바일 애플리케이션의 사용자 인증 및 관리를 제공하는 AWS 서비스

- 회원가입, 로그인, 로그아웃 기능을 손쉽게 구현
- JWT 기반 인증 지원
- 소셜 로그인 연동

![](Pasted%20image%2020260621234319.png)
### Congito 서비스

**User Pool**
> 어플리케이션 유저에 대한 로그인 기능 제공

- 사용자 계정 / 인증 기능
- 사용자 정보를 저장, 관리하는 사용자 디렉토리 역할
- 간단한 로그인 기능 제공 / MFA / 패스워드 재설정
- Google, Facebook, Apple 등 외부 IdP(소셜 로그인) 와 연동 가능
- API Gateway / ALB와 통합 가능

![](Pasted%20image%2020260621234158.png)


**Identity Pool (Federated identity)** 
> 인증된 사용자에게 AWS 리소스에 대한 접근 권한을 제공

- Cognito User Pool, Google, Apple, Facebook 등의 외부 IdP(Identity Provider)와 통합 가능
- 로그인에 성공한 사용자에게 임시 AWS 자격 증명(Temporary Credentials) 을 발급
- IAM Role과 연동하여 S3, DynamoDB 등의 AWS 리소스 접근 권한을 제어


https://docs.aws.amazon.com/cognito/latest/developerguide/what-is-amazon-cognito.html