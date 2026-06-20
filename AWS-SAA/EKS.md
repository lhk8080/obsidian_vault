# EKS
> k8s 클러스터를 쉽게 구축하고 운영할 수 있도록 제공하는 완전 관리형 쿠버네티스 서비스
- Elastic Kubernetes Service
- Master Node를 AWS가 대신 관리, 쿠버네티스 표준 환경이 필요할 때 사용
- Worker Node : 다양한 노드 타입을 지원
	- Management Node Groups
	- Self-Managed Node Groups
	- AWS Fargate
- Data Volume : CSI(Container Storage Interface) Driver 설치 -> PVC 생성해서 Pod에 마운트
	- EBS
	- EFS (Fargate와 연결)
	- FSx (for Lustre / for NetAPP ONTAP)