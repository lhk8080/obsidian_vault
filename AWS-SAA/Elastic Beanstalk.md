> AWS가 관리형어로 서버/로드밸런서/오토스케일링까지 다 구성
- Beanstalk 서비스를 사용하면 개발자가 애플리케이션만 올리면 됨
- 관리형 배포 플랫폼 (PaaS)
- EC2 생성 / Load Balancer 구성 / Auto Scaling 설정 / 배포 및 헬스체크 / 로그 관리 등을 수행 (Cloudformation templete 느낌)
- 다양한 언어 지원, 자동 배포 가능
- 세밀한 인프라 제어는 어렵지만, 서비스 특성상 복잡한 아키텍쳐가 요구되지 않을 때 사용
- Beanstalk 서비스 자체는 무료

### Elastic Beanstalk의 구성 요소
- Application : Beanstalk의 구성요소의 묶음, 단위
- Application Version : 
- Environmnet

(+) web server tier / worker tier