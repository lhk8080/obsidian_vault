## IAM Policy 구조
- Version : IAM Policy JSON 문서 양식 버전
- Id : Policy 식별자 (옵션)
- Statement : 1개 이상 존재할 수 있음
    - Sid : Statement id, optional
    - Effect : Allow or Deny
    - Principal : 정책이 적용될 대상 (account /user / role)
    - Action : 허용/차단할 실제 동작 (ex. GetObject, PutObject)
    - Resource : 정책을 적용할 리소스 목록
    - Condition : 정책이 적용될 수 있는 조건 (옵션)

## IAM Password Policy
- 강력한 정책 패스워드
    - 패스워드 최소 길이 / 패스워드 구성 형식을 정의
    - 패스워드 변경 주기
- Multi Factor Authentication - MFA
    - 패스워드 外 다른 인증 수단
    - 패스워드가 유출되어도 계정을 보호
    - ex. Virtual MFA(ex. Google Authenticator, Authy), U2F(실제 물리 키) 등등

## IAM Role

> AWS 권한 묶음을 담고 있는 권한 객체

- AWS 리소스에 권한을 주려면 role만들어서 알맞게 붙여줘야 함

|항목|IAM User|IAM Group|IAM Role|
|---|---|---|---|
|의미|실제 사용자 계정|User 권한 묶음|임시 권한 객체|
|로그인 가능|가능|불가능|직접 로그인 불가|
|Access Key 보유|가능|불가능|기본적으로 없음|
|권한 부여 대상|사람|여러 User|User/EC2/EKS/AWS 서비스|
|사용 방식|직접 사용|User에 연결|AssumeRole|

## IAM Security Tools
- IAM Credentials Report
    - 계정 전체 IAM 사용자 인증 정보 상태 점검
    - 비밀번호 / Access Key / Key Ratation / MFA
    - Credential report 탭에서 csv 파일 다운로드
- IAM Acess Advisor
    - user level → 특정 User/Role이 어떤 서비스를 사용했는지 확인
    - Least Privilege, 권한 축소 판단에 도움

## IAM 가이드라인

- root 계정은 account 셋업 말고는 사용하지 말 것
- physical user = AWS user로 매핑 → 여러 사람이 하나의 계정 공유 X
- strong password policy / MFA 설정
- CLI / SDK 사용을 위해서는 Access Key 생성하고 별도로 관리, Access Key는 절대로 공유되어서는 안됨
- 권한 심사를 위해서는 IAM Credential Report + IAM Access Advisor를 볼 것

---

# AWS Budget

### Billing and Cost Management
- AWS 계정에 대한 청구 정보를 확인할 수 있는 콘솔
- 서비스별 요금 / 활성화된 서비스 확인 가능 (EBS, EC2 …)

### Budgets and Planning
- AWS 비용/사용량에 대한 예산 설정
- 기준 초과 시 알림을 보내줌