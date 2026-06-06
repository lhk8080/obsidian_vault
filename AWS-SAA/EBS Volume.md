- Elastic Block Store
- EC2가 종료되어도 데이터를 유지
	- Delete on Termination 설정
- 프로비저닝, 생성 전 볼륨의 크기 IOPS를 지정
- EC2와 네트워크로 연결 > EC2와 같은 AZ에 존재 (AZ에 종속, 연결 시 약간의 지연이 존재)
	- 다른 AZ 영역에서 볼륨을 이동하려면 snapshot이 필요
- 하나의 EC2에만 붙을 수 있음 
	- multi attatch를 지원하지만, 여러 제약 사항이 있음, SAA 수준에서는 다루지 X)
	- EBS는 EC2에 꼭 붙어있을 필요는 없음 (unattatch 상태 ok)


## EBS Snapshot
- volume 백업을 위해 사용
- EC2 > EBS > Snapshot
- 다른 AZ/Region 간에 snapshot 내용을 복사할 수 있음 (snapshot / restore)
- 스냅샷 기능
	- EBS Snapshot Archive : archive tier, 75% 저렴, 24~72시간 이내에 볼륨 복구 가능
	- Recycle Bin for EBS Snapshot : 
		지워져도 recycle bin에 저장
		복구 가능 기간 지정 (하루~1년 사이)
		snapshot 콘솔 상단 > Recycle bin 버튼
	- Fast Snapshot Restor : FSR, 빠른 스냅샷 복원, full initialization snapshot > 빠른 복구, 대기시간 X, 비용이 많이 듦


## AMI
- Amazone Machine Image
- 자신만의 software, configuration, operating system, monitory 도구 선택 가능 > pre- packaging
- EC2 > 빠른 부팅 및 설정


## EBS Volume Type

EBS Volume : Volume Size, Throughput / IOPS (I/O Ops Per Sec) 등 결정

부팅 볼륨은 SSD 계열만 사용 가능

- gp2 / gp3 (SSD) : 범용, 가격-성능 균형, 부트 볼륨으로 사용
    - gp2 - 성능이 볼륨 크기에 따라 결정됨
    - gp3 - gp2 개선 버전, 볼륨 크기와 성능을 따로 정해줄 수 있음
- io1 / io2 / io2 Block Express (SSD) : 높은 성능 / *mission-critical / high-throughput
    - PIOPS(Provisioned IOPS SSD)
    - IOPS 퍼포먼스가 중요할 때 (ex. Database Server)
    - EBS Multi Attatchment 지원 (하나의 EBS 볼륨이 여러 EC2에 붙을 수 있음)
- st1 (HDD) : 저렴한 가격, throughput-intensive > 엑세스 빈도는 높으나 저렴한 가격을 원할 때
    - big data, data warehouse, *log processing 등에 사용
- sc1 (HDD) : 매우 저렴한 가격, 엑세스 빈도가 매우 적을 때 (Cold HDD)

*mission-critical : 장애/데이터 유실 발생 시 서비스에 심각한 영향을 주는 것(시스템/애플리케이션/데이터)

*log processing : 로그 수집/가공/분석하는 작업

### 스토리지 성능

스토리지 성능을 파악할 때에는 `IOPS` / `Throughput` / `Latency` 이 세 가지를 파악

- IOPS : Input/Output Operations Per Second
    
    초당 몇 번 읽고/쓰기를 할 수 있는지
    
    작은 파일/데이터를 많이 읽어야 할 때 중요
    
- Throughput : 처리량, 초당 몇 MB를 전송할 수 있는지
    
    큰 용량의 파일을 읽는 경우(영상 파일, 백업, 빅데이터 분석)
    
- Latency : 응답 시간, 요청 후 결과가 오기까지 걸리는 시간
    
    실시간 서비스에서 중요함
    

### EBS Multi-Attatch

> 같은 AZ에 존재하는 다중의 EC2 인스턴스가 동일한(하나의) EBS 볼륨을 붙여서 씀

- EBS type 中 **io1/io2 family**만 가능
- 부착된 모든 인스턴스가 read/write 권한을 가짐
- **최대 16개의 EC2 인스턴스에 부착** 가능
- EBS의 IOPS 및 Throughput은 모든 인스턴스가 공유하므로 I/O 경합(Contension)이 발생할 수 있음
- 일반 파일 시스템은 부적합(ext4, XFS) > **Cluster-Aware FS**을 사용해야 함 (Lock / Metadata)
- 클러스터링 어플리케이션 > 높은 가용성 확보 / 데이터 동기화
- Oracle RAC / SAP / Teradata

c.f) multi-attache vs NFS

*OLTP : 서비스 운영용 DB

*OLAP : 기업 데이터 분석용 DB

---

## EC2 Instance Store

> EC2 서버에 직접 연결된 로컬 디스크, 임시적

- EBS는 **네트워크**를 통해 EC2에 붙음 > 성능에 한계가 있음
- 높은 입출력 성능을 원할 때에는 EC2 Instance Store를 사용
- **EC2 인스턴스가 종료될 경우 > Instance Store 스토리지 손실됨** (ephemeral)
- buffer, cache, *scratch data, temporary content

*scratch data : 작업을 처리하는 과정에서 잠깐 사용하는 임시 데이터(중간물)

---

## EBS Encryption

> 디스크에 저장되는 데이터를 암호화해서 저장, 읽을 때 복호화함 (데이터 노출 대비)

- KMS 기반 암호화 (**AES-256**)
- EBS Volume, Snapshot, AMI까지 암호화 적용
- 암호화는 latency에 영향을 거의 주지 않음 (Nitro 기반 인스턴스 > 암호화와 관련된 성능 오버헤드가 거의 없음)

(+) 암호화되지 않은 EBS 볼륨을 암호화 :

1. 기존 볼륨 스냅샷 생성
2. **스냅샷 복사+ 암호화 옵션 주기**
3. 암호화된 스냅샷으로 새 볼륨 생성

---

# AMI

- Amazone Machine Image
- **Region 단위 리소스**, 복사를 통해 리전 간 공유 가능
- EC2 인스턴스의 커스터마이징 > 자신만의 소프트웨어, 설정값, OS, 모니터링 등 > pre-packaging
- public AMI / 본인만의 AMI / AWS Marketplace AMI (사고팔기 가능)
- AMI 생성 : EC2 시작 > Customize > Stop instance > Build AMI (EBS Snapshot)

---

# Amazone EFS

- Elastic File System
- Managed NFS(Network File System), 여러 EC2에 **mount**
- **multi-AZ 지원 (*mount target)**/ security group으로 접근 제어 / Linux 기반 AMI만 지원 / KMS 암호화 지원
- 높은 가용성, 확장성, 비쌈, 사용량 당 청구(pay-per-use, 자동으로 FS 크기 확장) > 별도로 프로비저닝 필요 X
- EFS 생성 :
    1. File System setting : Regional/One Zone (mount target), 자동 백업, Lifecycle management, performance setting
    2. Network Setting : VPC, Mount Target(AZ/ Subnet/ SG),
    3. File System Policy : EFS에 대한 Resource Policy(IAM Role기반 접근 제어, 읽기 전용 마운트 등등)

*mount target : EFS에 접근하기 위한 네트워크 엔드포인트(ENI)

### EFS Scale

**용량과 성능을 사용량에 맞춰 자동으로 확장**

- 사용자는 용량 제한을 신경쓰지 않아도 ok (capacity planning 필요 X)
- EFS에 마운트하는 클라이언트 수가 증가하면 알아서 성능도 확장

### EFS Performance Mode

> 어떤 성능 특성을 가질 것인가?

- General Purpose : 디폴트값 / 낮은 지연 시간 / 대부분의 워크로드
- Max I/O : 처리량 최대화 / 병렬 처리량 good / but general에 비해 지연 시간

### EFS Throughput Mode

> 처리량을 어떻게 제공할 것인가?

- Bursting : 저장된 데이터 양에 비례해서 처리량 제공, 디폴트값,
- Provisioned : **필요한 처리량을 직접 지정,** 일정한 처리량 보장, 예측 가능한 고성능 워크로드
- Elastic Throughput : 권장, 실제 사용량에 따라 처리량 확장/축소, 신규 워크로드에 적합

### EFS Storage Class

> 파일의 접근 빈도에 따라 저장 계층을 분리 → 저장 비용 최적화, 약 90%까지 비용 절감 가능

**Lifecycle Policy**을 참조하여 스토리지 티어가 옮겨짐

- Standard : 자주 접근
    
- Infrequent Access(EFS-IA) : 가끔 접근,
    
- Archive : 거의 접근하지 X(1년에 몇 번), 50% 이상 저렴
    
- EFS와 Availability 옵션
    
    - Regional - 여러 AZ에 데이터를 복제해둠, 운영 환경에서 권장
    - One Zone - 한 AZ에만 데이터 저장, 개발 환경
