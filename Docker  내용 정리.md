## Docker  내용 정리

### 

### 1. docker을 사용하는 이유

- 배포과정에 있어서 다양한 프로그램을을 합치게 되는데 이와 중에 각각의 버전 등으로 인한 에러를 한 번에 해결함
- docker, jenkins, kubernates 등을 연결하면 release system을 쉽게 자동화하고 배포, 운영을 효율적으로 할 수 있음
- Devops

### 2. docker을 사용하기 위한 준비

- AWS EC2 - 서울 서버
- EC2에 ubuntu20.04LTS를 설치 -> 전부 ok 클릭 -> 마지막에 생성하는 key는 알아서 잘 보관 -> 생성된 EC2에 탄력적 IP를 연결(running instance) -> 성공적으로 연결하면 이제 고정된 IP로 접근가능
- EC2를 windows에서 접근하는 방법
  - putty 설치
  - puttygen으로 아까 보관해 놓은 key를 이용해서 pem키 만들기 -> 같이 잘 저장
  - putty로 IP, name, 사용자, key등록을 한 뒤에 저장 -> 이후에는 putty에서 클릭 한번으로 EC2에 연결가능

