## AWS access

- AWS에 접근하는 방법은 총 3가지
    1. Management Console → password + MFA
    2. Command Line Interface → **Access Keys**
        터미널에서 AWS에 접속
    3. Software Development Kit(SDK, for code) → **Access Keys**
        

### Access Key

- Access Key는 aws 콘솔에서 생성할 수 있음
- Access Key는 보안에 있어 중요함, 절대 타인과 공유하면 X
- Acceess Key ID / Secret Access Key
- Secret Access Key는 생성 시 1번만 노출됨 → 시크릿 키 잃어버리면 기존 키 삭제하고 새 키 생성해야 함
- IAM User당 최대 2개까지만 생성 가능 (why? Key Rotation)
- 장기 Access Key 최소화
    현재에는 Access Key 사용을 줄이기 위해 다음과 같이 접근 제어를 함
    - IAM Role
    - EC2 Instance Profile
    - EKS IRSA
    - AWS SSO
    - Temporary Credential (STS)

## AWS CLI
- 터미널에서 AWS 서비스를 제어할 수 있게 해주는 도구 (AWS API 조회)
- `aws [service] [action]` 형태의 명령어
    - ex) `aws s3 ls` → s3 버킷 목록 조회
    - ex) `aws ec2 describe-instances` → EC2 인스턴스 조회
- CLI는 IAM 자격 증명을 사용
    - `aws configure` 명령어로 자격 증명 설정
    - AWS Acess Key ID / Secret Access Key / Default Region / *Output format
    - 설정파일은 `~/.aws` 에 위치
- CLI를 활용하면 스크립트를 짜서 리소스를 관리/작업을 자동화 할 수 있음 → CI/CD에서 자주 사용됨
    
- (+) aws config profile
    여러 aws 계정을 분리해서 관리 가능
    ex) `aws configure —profile prod`
    
- *Configure Output Format
    AWS CLI 명령 결과를 출력 관련 옵션
    - JSON
        - 가장 많이 사용
        - 자동화 / 스크립트 친화적
        - *jq와 같은 도구와 같이 사용 가능
    - yaml
        - json 보다 가독성 good
    - text
        - 스크립트 처리가 간단함 > bash에서 바로 grep, bash 단독 처리에 good(간단한 쉘 작업)
    
    (+) table, yaml-stream
    *jq : json 데이터를 가공/필터링하는 cli 도구
    

## AWS SDK
- Language-specific API (라이브러리 형태로 제공)
    - python - boto3 / Java - AWS SDK for Java …
- 응용 프로그램 안에서 언어에 맞는 코드로 aws 리소스에 접근(embeeded)
- 다양한 형태로 지원 (Mobile, IOS, IoT Deice SDK)

## AWS CloudShell
> AWS Console > 브라우저 기반 Linux 터미널 환경

- 브라우저에서 AWS CLI를 사용
- 콘솔, GUI는 직관적이고 초보자 친화적이나, 반복 작업이나 필터링 자동화 측면에서 불편할 수 있음 > CLI를 이용하면 간단하게 처리 가능
- 미지원 리전도 존재


#### AWS CLI 참고
https://docs.aws.amazon.com/cli/latest/