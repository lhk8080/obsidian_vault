> 서버리스 데이터 통합 서비스

- 여러 데이터 소스 > 데이터를 추출(Extract) / 변환(Transform) / 적재(Load) ... 줄여서 ETL
- Serverless, ETL 자동화
- Data Catalog을 통해 메타데이터를 중앙에서 관리


![](Pasted%20image%2020260626211922.png)

---

### Glue Data Catalog
> 데이터의 메타데이터를 저장, 관리하는 중앙 저장소

### Glue Data Crawler
> 데이터 소스를 스캔하여 스키마를 자동 생성 > Data Catalog에 등록

- S3, RDS 등의 데이터 소스를 탐색
- 데이터 형식을 자동 인식 > 테이블 및 스키마 자동 생성


![](Pasted%20image%2020260626212419.png)

---

(+) Glue Job Bookmarks : 오래된 데이터 재처리 방지
(+) Glue DataBrew : 선행 변환을 통하여 데이터를 정제하고 정규화함
(+) Glue Studio : GUI 방식으로 ETL 파이프라인을 설계/관리
(+) Glue Streaming ETL : Kinesis, Kafka 등의 스트리밍 데이터를 실시간으로 처리