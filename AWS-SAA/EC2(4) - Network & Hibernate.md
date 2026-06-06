# Elastic Network Interface
- 가상 네트워크 카드(NIC) → EC2에 부착 (eth0, eth1)
- 특정 서브넷과 바인딩됨
- Priavte IP / Public IP(EIP) / MAC Address / Security Group / Subnet 정보 (EC2 네트워크 설정 묶음)
- EC2에 ENI를 붙였다 뗐다 할 수 있음 → 장애 조치(fail over)에 good
- EBS와 마찬가지로, ENI는 EC2와 독립적인 수명을 가질 수 있음 (EC2가 삭제되어도 ENI는 남길 수 있음)

### Primary Priviate IP
- ENI 생성 시 반드시 존재하는 기본 IP
### Secondary Private IP
- ENI에 추가로 붙이는 IP (보조 IP)

**Reference**
[New – Elastic Network Interfaces in the Virtual Private Cloud | Amazon Web Services](https://aws.amazon.com/ko/blogs/aws/new-elastic-network-interfaces-in-the-virtual-private-cloud/)

---
# EC2 Hibernate
> EC2 상태를 EBS에 저장 > 빠른 시작, OS 중단
- Stop → EBS 유지
- Terminate → root EBS는 삭제, EC2와 같이 삭제 옵션 주지 않은 EBS는 생존
- Start → OS 부팅 / User Data 스크립트 실행 / 응용 프로그램 실행 / 캐시 웜업
- Hibernate → **RAM이 유지**됨, booting이 빨라짐(OS가 restart) / **OS 관점에서는 인스턴스 중단 X**
- Hibernate 기간은 60일을 넘겨서는 안됨
- EC2 생성 中 고급 옵션에 Hibernate Behavior 선택
    - RAM 상태를 EBS 볼륨에 저장되어야 되므로, 용량이 맞아야 함
    - RAM 데이터를 읽을 수 없도록 암호화 설정이 되어 있어야 함(EBS Volume Enscription, EBS 생성 시 암호화 옵션 선택)

*Hibernate : 동면하다