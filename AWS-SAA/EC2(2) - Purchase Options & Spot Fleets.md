# EC2 구매 옵션
### On-Demand
- 사용한 만큼만 과금하는 기본 방식
- 약정 없음
- 테스트/개발/단기 운영에 적합
### Reserved Instance (RI)
- 1~3년 사용 약정으로 할인 받는 방식
- 항상 켜두는 서버에 적합
- On-Demand 대비 비용 절감 가능
### Savings Plan
- 일정 사용 금액을 약정하고 할인 받는 방식
- EC2 타입 변경에도 비교적 유연
- RI보다 운영 유연성이 높음
### Spot Instance
- AWS 남는 자원을 매우 저렴하게 사용
- 필요 시 AWS가 회수 가능
- 배치 작업/AI 학습 등에 적합
### Dedicated Host
- 물리 서버 자체를 단독으로 사용
- 서버 단위 가시성 제공
- 라이선스/규제 대응 시 사용
### Dedicated Instance
- 다른 고객과 물리 서버를 분리해서 사용
- 하드웨어는 AWS가 관리
- 격리가 필요한 환경에 적합
### Capacity Reservation
- 특정 AZ에 EC2 용량을 미리 확보
- 갑작스러운 자원 부족 방지
- 중요 서비스 안정성 확보 목적 사용
- 실제로 예약한 인스턴스를 사용하지 않아도 요금은 부과
## Spot Instance
- 최대 90% 저렴하게 인스턴스 사용 가능
- 배치 작업, 데이터 분석, 장애 복원력이 있는 워크로드에 적합
- Spot의 가격은 수요/공급에 따라 변동됨 → AZ, 리전, 인스턴스 타입 별로 다 다름
- 사용자는 Spot Request에서 “최대 지불 가능 가격”을 설정 → 최대 가격이 현재 spot 가격보다 높으면 인스턴스 중단, 2분 동안의 유예 시간이 있음(저장, 프로그램 종료)

### Spot Request
> spot instance를 요청하는 설정값
- 어떤 인스턴스를 얼마나 사용할 것인가?
- Max prices → 사용자가 지정한 최대 가격 , 현재 spot 가격과 비교해서 인스턴스 사용 여부 결정
- Request Type
    - one-time : 한 번만 실행, 인스턴스 종료되면 끝
    - persistent : 인스턴스가 중단되도, spot 용량 생기면 재실행
- Request state
    - open : request는 생성, but spot instance는 실행 X
    - active : request 조건이 만족되어 spot instance가 실행중인 상태
    - disabled : 사용자가 request를 비활성화, request에 의해 인스턴스는 생성되지 않으나, 이미 실행중인 인스턴스는 자동 종료 안될 수 있음
    - cancelled : request 자체가 삭제
    - closed : one-time request가 완료된 상태

![[Pasted image 20260606133141.png]]

# Spot Fleets

> 여러 종료의 Spot 인스턴스 + 온디맨드 인스턴스(옵션)
- 여러 종류의 스팟 인스턴스를 조합해서, 목표 용량을 저렴하고 안정적으로 확보함
- 필요에 따라 온디맨드 인스턴스도 섞어서 사용
- lanch pool →spot fleet이 조건에 맞는 pool을 선택

### Allocation Strategy
- LowestPrice
    - 가장 싼 pool은 경쟁이 심해서 중단 가능성이 높음
- Diversified
    - 여러 pool에 분산 실행, 특정 pool 장애 영향 감소
    - 긴 작업 / 가용성이 중요할 때
- CapacityOptimized
    - 가장 남는 용량이 많은 pool 선택
    - 중단 가능성 최소화 → 안정성 Good
- priceCapacityOptimized
    - 용량 안정성 고려 → 그 중 저렴한 Pool 선택

c.f) 안정성 vs 가용성