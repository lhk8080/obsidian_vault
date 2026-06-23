
# Software Updates Offloading
> 소프트웨어 업데이트 파일 배포 작업을 원본 서버 대신 Cloudfront가 처리

- 어플리케이션 설치 파일 / 패치 파일 등을 CloudFront가 캐싱하여 사용자에게 전달함
- 사용자 -> 가까운 Edge에서 파일 다운로드
- 원본 서버의 네트워크/CPU 부하를 줄임
- 정적인 콘텐츠 배포

*Offload -> 원래 해야 할 일을 다른 시스템에게 넘겨줌