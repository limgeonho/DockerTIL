## Docker  내용 정리

### 

### 1. docker를 사용하는 이유

- 배포과정에 있어서 다양한 프로그램을을 합치게 되는데 이와 중에 각각의 버전 등으로 인한 에러를 한 번에 해결함
- docker, jenkins, kubernates 등을 연결하면 release system을 쉽게 자동화하고 배포, 운영을 효율적으로 할 수 있음
- Devops

### 2. docker를 사용하기 위한 준비

- AWS EC2 - 서울 서버
- EC2에 ubuntu20.04LTS를 설치 -> 전부 ok 클릭 -> 마지막에 생성하는 key는 알아서 잘 보관 -> 생성된 EC2에 탄력적 IP를 연결(running instance) -> 성공적으로 연결하면 이제 고정된 IP로 접근가능
- EC2를 windows에서 접근하는 방법
  - putty 설치
  - puttygen으로 아까 보관해 놓은 key를 이용해서 pem키 만들기 -> 같이 잘 저장
  - putty로 IP, name, 사용자, key등록을 한 뒤에 저장 -> 이후에는 putty에서 클릭 한번으로 EC2에 연결가능

### 3. docker를 사용하기 전 알아야할 기본적인 Linux

1. 명령어

   - root : 관리자 id
   - sudo : 관리자 권한으로 실행
   - pwd : 현재 디렉토리 위치
   - ls, ls -al : 현재 디렉토리 파일 목록, 숨김까지 전부
   - chmod -R 777 dir : 하위 폴더 전체까지 권한 777 부여
   - cat : 파일 보기
   - rm : 파일 삭제
   - rm -rf : 완전 삭제

2. Stream

   - stdin : 표준 입력

   - stdout : 표준 출력

   - stderr : 표준 에러

   - 리다이렉션 

     - <, >

       ls -al > test.txt : 현재 디렉토리목록을 test.txt에 저장

     - <<, >>

       기존의 것에 내용을 추가

     - |

       ls -al | grep bash : ls -al한 출력결과를 grep의 입력으로 전달

3. process 관리

   - foreground

     - 해당 명령을 실행하고 종료까지 다른 입력 불가

   - background

     - 말 그대로 background로 실행 -> 명령어 뒤에 &기호 추가

   - pid == processID

   - ps : 유저가 직접 실행시킨 process

   - ps aux : 시스템 전체 실행중인 process(aux는 옵션)

     ps aux | grep bash : 실행중인 process 중에 bash가 들어간게 있는지?

     kill -9 PID : PID 강제 종료(-9는 강제종료 옵션)

4. 하드링크, 소프트링크

   - 하드링크
     - ln A, B : 완전 동일하게 연동
   - 소프트링크(심볼릭링크)
     - ln -s b.txt c.txt : b.txt의 바로가기를 c.txt라고 설정
   - cp A B : A파일을 B파일로 복사(cp == copy)
   - cp -rf * 폴더명 : 현재 dir의 하위를 전부 폴더명에 복사함

5. 우분투 사용법

   - sudo apt-get update : 최신으로 업데이트
   - sudo apt-get upgrade : 업그레이드(함부로 하지 말것!! => 버전이 달라져서 안 될 수도 있음)
   - sudo apt-get install 패키지명 : 패키지 설치

6. Vim 에디터

   - 일반모드
     - vi test.txt : test.txt의 이름으로 파일을 만들고 들어가기
   - 입력모드
     - i : insert 입력 -> esc : 다시 일반모드, x : 삭제
   - 명령모드
     - : : 명령모드, :wq : 쓰고 저장 후 나오기

### 4. docker, docker-compose 설치

 1. curl -fsSL https://get.docker.com/ | sudo sh : 도커 설치

 2. sudo apt-get install curl : curl이 없다면 설치

 3. sudo usermod -aG docker $USER : 현재 사용자에게 권한 주기 

    => 이제부터 cli에서 docker명령어 사용가능

### 5. docker의 주요 구성 요소

- docker engine : docker command -> docker demon process에서 resp api로 통신함

- docker image : 컨테이너를 생성하기 위한 명령들(script)을 모은 템플릿(script들을 쌓는다 layer로)

- docker container : image를 통해서 만든 것

- 결론 : docker image를 쌓아서 docker container로 만든 뒤에 동작

- 명령어[image]

  1. docker hub에 회원가입
  2. docker login : username과 password를 입력 후 로그인
  3. docker logout : 로그아웃
  4. docker search ubuntu : ubuntu 검색, official에 [OK]면 공식 이미지
  5. docker pull ubuntu : 이미지 다운로드
  6. docker pull ubuntu:20.10 : 20.10버전의 ubuntu 다운
  7. docker images : 다운받은 이미지 조회
  8. docker images ls -q : 다운받은 이미지의 id만 출력
  9. docker rmi 이름:태그 : 삭제
  10. docker rmi 이미지ID : 삭제

- 명령어[container]

  1. docker create ubuntu : 컨테이너 생성

  2. docker ps : 실행중인 컨테이너 확인

  3. docker ps -a : 실행중 + 생성된 컨테이너까지 전부 조회

  4. docker ps -a -q : 전체 컨테이너의 id조회

  5. docker rm 컨테이너ID : 삭제

  6. docker create --name myubuntu ubuntu : myubuntu라는 name으로 ubuntu 이미지를 컨테이너로 생성

  7. docker start 컨테이너이름 : 컨테이너 실행

  8. docker run 옵션

     - -i : 컨테이너 표준입력을 열기
     - -t : 가상 터미널
     - -it : 주로 -it 사용, 컨테이너에 키보드입력 열어놓기
     - -rm : 종료시(exit) 삭제
     - -d : 백그라운드로 실행

  9. docker run -it ubuntu : ubuntu이미지를 컨테이너로 만들어서 실행

     -> /# : 컨테이너를 실행하고 해당 컨테이너로 들어간 상태

  10. docker stop 컨테이너ID : 해당 컨테이너 종료하기

  11. docker 명령

      - pause : 중지
      - unpause : 다시실행
      - start : 실행
      - restart : 껐다가 다시 실행

  12. docker run -d --name apacheweb httpd 

      -> apacheweb란 이름으로 httpd이미지를 받아서 컨테이너로 만들고 백그라운드로 실행

      -> 하지만 포트가 연결되어 있지 않기 때문에 접근불가!!!

  13. 포트포워딩 : host(ec2)로 들어온 포트를 내부의 컨테이너 포트로 연결

      docker run -d -p 9999:80 --name apacheweb httpd

      -> IPv4주소:9999로 접속하면 It works! 페이지

      -> 해당 페이지를 바꾸려면?

      -> FileZilla로 원하는 index.html을 ec2에 업로드하고 컨테이너 내부 파일과 연결한다.

  14. 최종 명령

      docker run -d -p 9999:80 -v /home/ubuntu/index.html:/usr/local/apache2/htdocs --name apacheweb httpd

      -> 9999와 80 포트 연결, -v(바인딩)옵션으로 host(ec2)의 절대경로 : 컨테이너의 절대경로 연결

- 명령어[기타]

  1. docker df : docker 사용용량 확인

     alpine태그가 붙어 있으면 경량 버전

  2. docker container stats : 실행중인 컨테이너 사용 리소스 확인

  3. docker exec -it apacheweb /bin/bash : apacheweb의 컨테이너로 들어가기

     alpine는 /bin/sh로 들어감

  4. 한 번에 컨테이너 중지, 삭제, 이미지 삭제하기(복붙)

     ```bash
     docker stop $(docker ps -a -q)
     docker rm $(docker ps -a -q)
     docker rmi -f $(docker images -q)
     ```


### 6. Dockerfile

​	1. 