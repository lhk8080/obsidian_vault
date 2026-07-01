# Container 
> 애플리케이션 + 실행 환경을 패키지화해서, 환경적 제약을 없애는 가상화 기술
- 개발/테스트/운영 환경 간의 차이를 최소화
- 컨테이너들은 호스트 OS의 커널을 공유 (VM 보다 가볍고 빠름)
- 빠른 배포, 높은 이식성, 쉬운 확장성
- Docker, Containerd, Podman, CRI-O 등의 기술
(+) containerd, runc > 컨테이너 런타임
(+) AWS의 컨테이너 기술 관련 서비스 > EKS, ECS, Fargate, ECR




[ECR](ECR.md)
[EKS](EKS.md)
[ECS](ECS.md)