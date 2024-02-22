# jenkins auto build
- 젠킨스를 이용하여 빌드 작업을 자동화 하기


# Architecture
![image](https://github.com/yeomyaloo/jenkins-auto-build/assets/81970382/b1dca453-1c6e-437f-8c1e-7e491cfcbfdf)



# Process
## 1. EC2 생성
- 본인 AWS 계정으로 EC2를 생성해주세요

## 2. putty나 mobaxterm을 이용한 EC2 환경 연결하기
![image](https://github.com/yeomyaloo/jenkins-auto-build/assets/81970382/ebfe4c22-2bf8-4a6a-82c1-0b7e7dc0fa3c)

## 3. EC2 리눅스 환경에서 도커를 이용한 젠킨스 실행 및 설치
#### 3-1. 젠킨스 설치 전에 apt 업데이트 업그레이드 그리고 도커 설치
- `sudo apt update`
- `sudo apt upgrad`
- `sudo ducker install docker.io`
#### 3-2. 도커안에 젠킨스 실행 및 설치 명령어
- `docker run --name myjenkins --privileged -p 8080:8080 jenkins/jenkins:lts-jdk17tomca`
  - 젠킨스 도커 이름을 myjenkins로 지정
  - 권한 문제를 해결하기 위하여 privileged 옵션 사용
  - 포트 번호를 8080으로 가진 젠킨스와 도커에서 사용할 포트번호를 8080으로 동일하게 매핑
  - 다운 받을 젠킨스 이미지 버전 명시

#### 3-3. 명령어 입력 후에
- 위의 명령어를 입력 받은 뒤라면 젠킨스가 실행될 것이에요.
- 또한 콘솔에 해당 젠킨스 어드민 계정에 접근 가능한 비밀번호가 주어집니다.
- 이를 이용해서 젠킨스 웹 페이지에 접근해서 접속해봅시다.

> 이미 젠킨스 컨테이너가 깔려 있다면?<br>
> `docker start <컨테이너_명 혹은 컨테이너_아이디>`: 컨테이너를 실행 시켜줍니다.<br>
> `docker logs -f <컨테이너_명 혹은 컨테이너_아이디>`: 로그를 확인하는데 이때 젠킨스 어드민 계정에 접근 가능한 비밀번호도 콘솔에 함께 출력 됩니다.

## 4. 젠킨스 웹 접속
![image](https://github.com/yeomyaloo/jenkins-auto-build/assets/81970382/62d78513-86d7-484f-a6da-5f11facd583d)
- EC2 퍼블릭 아이피를 이용해서 도커에서 젠킨스를 다운 받을 때 매핑한 포트를 조합해 URL을 입력하면 위와 같이 젠킨스에 접근 가능한 웹페이지가 뜹니다.
  - ex) http://EC2_public_ip:8080/

#### 4-1. 파이프라인 생성
- new item을 누른 후 




  
