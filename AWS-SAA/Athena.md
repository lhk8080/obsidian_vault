> Serverless한 쿼리 서비스

- 데이터에 저장된 데이터를 서버 구축 없이도 조회 가능
- 일반적으로, 데이터 분석을 위해서는 DB를 구축 
- Athena 서비스는 DB 구축 없이 S3에 데이터를 저장해서, 쿼리/분석 가능하게 만들어 줌
- 다양한 파일 포멧 지원 (csv, json, orc 등)
- 보통 `Amazone QuickSight` 와 같이 사용됨 (분석 리포트/대시보드)
- 사용법 :
	- 데이터를 S3에 저장
	- 스키마 등록
	- Athena에서 SQL 실행
	- 결과 확인

```
csv / json / parquet 파일 -> S3 bucket -> Athena (SQL 실행) -> 분석 결과
```

### Athena Performance Improvement

-> 아테나는 **스캔한 데이터 양을 기준으로 과금**됨
-> 성능 최적화 = 비용 절감

- Apache Parquet, ORC 사용
	- AWS 서비스 중 `Glue`를 사용하면 데이터를 Parquet, ORC로 변환 가능
- 데이터 압축
	- S3에서 읽는 데이터 감소
	- Gzip, Bzip2 , Snappy 등
- 파티션 사용
	- 데이터 분리
	- 디렉토리를 분리해서 필요한 폴더만 읽을 수 있게 함
- 작은 파일이 너무 많지 않게 함
	- 파일 별 메타 데이터 조회 > 오버헤드 증가
	- 파일 수는 적게, 파일 크기는 크게 가져감 (128MB 이상)

### Federated Query
> 외부 데이터 소스를 S3로의 데이터 이동 없이 SQL로 직접 조회해서 통합 분석 할 수 있게 함

- Athena는 기본적으로 S3에 대한 쿼리만 가능
- Federated Query를 쓰면 S3 뿐만 아니라 외부 데이터 소스(온프레, RDS)까지 통합 조회 가능
- 여러 데이터 소스를 한 번에 분석
- **Data Source Connection** - 어떤 데이터 저장소에 접속해서 데이터를 읽어올 지 정의
	- Athena / 데이터 소스 중간에 Lambda 커넥터가 들어감
	- Athena > SQL을 Lambda에게 전달 > DB 접속 and 결과 반환


![](Pasted%20image%2020260624220303.png)