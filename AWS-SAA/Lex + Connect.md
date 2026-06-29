

- Amazon LEX
	- 아마존 알렉사와 같은 기술
	- 음성를 텍스트로 변환(ASR, Automatic Speech Recognition) + 자연어 이해(NLU, Natural Language Understanding)
	- 챗봇, 콜센터 봇 만들기에 활용 가능
- Amazon Connect
	- cloud-based virtual contect center
	- 전화 수신/발신 + 상담원 연결 + 자동 음성 안내 + 실시간 통화 모니터링 등
	- 일반적인 콜센터 운영보다 80퍼센트 저렴

```
call -> [Connect] -> [LEX] -> [Lambda] -> [CRM]
```

*CRM : Customer Relationship Management, 고객 정보 관리, 매출과 고객 만족도를 높이는 시스템