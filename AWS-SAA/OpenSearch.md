> 대용량 데이터를 실시간으로 검색, 분석, 시각화 하기 위한 관리형 서비스

- 다른 DB와 상호보완적으로 같이 사용함
- Elasticsearch를 포크해서 만든 서비스
	- Full Text Search 지원 : 키워드, 유사 문장, 자동 완성, 오타 허용 등
	- Log Analytics : 검색 + 집계 작업 수행
	- Inverted Index : 빠른 검색 성능 제공
- managed cluster / serverless cluster 지원
- OpenSearch Dashboard 지원에 

**Usecase** 
- 보안 분석(SIEM)
- 상품 / 게시글 / 문서 검색
- 로그 분석
	