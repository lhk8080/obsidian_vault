
# ECS
> AWS의 관리형 컨테이너 오케스트레이션 서비스
- Elastic Container Service
- 컨테이너의 배포, 스케줄링, 로드밸런싱, 오토스케일링 등을 자동 관리
- Launch Type : 컨테이너를 어떤 인프라 환경에서 올릴 지 결정하는 옵션
	- EC2 Launch Type : **EC2 프로비저닝** 필요, EC2 Cluster에 EC2 인스턴스를 등록
	- Fargate Launch Type : 별도의 프로비저닝 X, AWS가 컨테이너 실행 환경을 **서버리스**한 형식으로 제공
- EC2 Tasks : 실행 최소 단위
	- Task Definition 기반 생성, 실행되는 컨테이너의 묶음을 의미
	- Task 별로 Role을 붙여 알맞은 AWS 서비스 접근 가능
	- 여러 Task가 동일한 파일을 사용할 경우 EFS를 공유 스토리지로 연결
- Service : Task를 원하는개수 만큼 지속적으로 유지/관리
- Service Auto Scaling : 트래픽/리소스 사용량에 따라 **Service의 Task를 자동으로 증가 or 축소**
	- Target Tracking (목표 지수 기반 Task 수 조절) / Step Scaling / Scheduled Scaling
	- EC2 Launch Type의 경우에는, ECS Cluster Capacity Provider / ASG를 통해 인스턴스 수도 조정해야 함

ECS Cluster 생성 -> Task Definition 생성 -> Task 생성 (+ Service)







