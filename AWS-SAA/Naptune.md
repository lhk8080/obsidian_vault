> 완전 관리형 그래프 데이터베이스

- 데이터 간 복잡한 관계 분석 > Graph DB
- Vertex / Edge 구조의 데이터
- HA / Multi-AZ / 백업
- usecase : 
	- SNS 
	- 추천 시스템
	- 사기(Fraud) 탐지
	- 권한 관리 시스템
- **Streams** 기능 : 데이터 변경 사항을 캡쳐
	- 데이터 변경 이력을 순서대로 기록
	- 스트림 데이터(기록)는 HTTP REST API로 접근 가능
	- 그래프에 변화가 생겼을 때 > 알맞은 작업 진행