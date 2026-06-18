### AWS Snow Family
> 인터넷 대신, 물리 장비를 이용해 데이터 이전/엣지 컴퓨팅 수행

#### Snowball
- 온프레미스에 저장된 대용량 데이터를 AWS로 마이그레이션을 진행할 때
- Snowball 장비 > 데이터 저장 > AWS에 반송 > S3에 업로드
#### Snowball Edge
- Snowball + 컴퓨팅 기능 추가
- 인터넷이 불가한 환경에서도 Lambda 실행, EC2 실행 가능

(+) Snowball을 통해 데이터를 S3 Glacier로 이전하려면? : Snowball > S3 > Glacier (with lifecycle policy)

### Amazone FSx
> AWS가 제공하는 완전 관리형 파일시스템 서비스
- FSx for Windows : 윈도우 파일 서버 지원, SMB, NTFS, Linux EC2가 마운트 가능
- FSx for Lustre : 고성능 컴퓨팅(HPC), IOPS 극대화
- FSx for NetApp ONTAP : NetApp NAS, NFS, SMB, ISCSI, 
- FSx for OpenZFS : 리눅스용 고성능 파일시스템

(+) EFS vs FSx

#### FSx File System Deployment Options
FSx for Lustre은 배포 옵션이 존재함
- Scratch File System : 빠른 성능, 데이터가 사라져도 다시 만들 수 있는 경우
- Persistent File System : 장기간 사용하는 영구 파일 시스템

#### Storage Gateway
> 온프레미스 스토리지와 퍼블릭 클라우드 스토리지를 연결하는 게이트웨이
- DR / Backup / Tiered Storage / on-premise cache + low latency file access
- 오래된 데이터나 자주 사용되지 않는 데이터는 값싼 클라우드 환경에 두는 게 비용효율적
- 사내 시스템은 유지하되, 데이터를 클라우드 영역에서도 확장
- 3가지 타입 : 
	- S3 File Gateway : SMB (Windows Active Directory와의 연동)/ NFS 프로토콜
	- Volume Gateway : iSCSI 프로토콜, cached/stored volumes
	- Tape Gateway : VTL(Virtual Tape Library)

#### Transfer Family
> 파일 전송 프로토콜을 사용하면서(기존 시스템 유지), 파일 전송 서버를 AWS로 옮김
- Client -> AWS Transfer Family -> S3/EFS 형태로 연결
- SFTP, FTPS, FTP 프로토콜을 지원

#### DataSync
- 온프레 환경/다른 클라우드/AWS 환경에서의 데이터를 다른 AWS 환경에 동기화 시킬 때 사용
- AWS가 아닌 환경에서의 동기화는 별도의 에이전트 설치가 필요함
- S3 / EFS / FSx 등에 동기화됨
- 데이터 동기화 주기는 시간/일/주 별로 설정 가능 (**예약 작업**)
- 파일의 **권한 및 메타데이터도 보존됨**



(+) FTP vs NFS
- FTP : 파일 **전송** 프로토콜, 파일 복사
- NFS : 파일 시스템 **공유**, 원격 디스크를 마운트해서 로컬처럼 사용
(+) iSCSI
- Internet Small Computer System Interface
- 원격 저장장치를 네트워크를 통해 서버의 하드디스크처럼 연결함
- SAN(Storage Area Network)의 구현체