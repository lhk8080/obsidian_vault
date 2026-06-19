
### Keep-Alive
> TCP 연결을 바로 끊지 않고 재사용
- 새로운 TCP 3-way handshake 과정을 생략
- 매번 연결을 새로 맺지 않으므로, 응답 시간 단축/메모리-CPU 사용량 감소
- 운영체제 관점에서는 소켓을 재사용 하는 것
- Keep-Alive Timeout : 연결을 유지하는 시간, 요청이 없으면 커넥션을 끊음


### LoadBalancer
- LB에서 Keep-Alive를 설정해놓으면 성능이 좋아짐
-  But, 로드밸런서-백엔드 서버 간의 타임아웃 설정 불일치 때문에 간헐적으로 에러 발생
- 백엔드는 연결을 종료했는데, LB는 연결이 살아있다 판단하면 ->  Connection reset, Bad Gateway 에러 발생
- 해결책 : 뒤쪽 서버의 timeout 값을 여유값을 둬서 더 길게 설정
