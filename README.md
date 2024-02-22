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
- [해당 블로그 포스팅을 이용해서 파이프 라인 생성 등을 직접 해주세요](https://narup.tistory.com/224)

#### 4-2. 젠킨스를 이용한 자동 빌드에 사용한 파이프라인 스크립트
```
pipeline {
    agent any

    stages {
        stage('git') {
            steps {
                git branch: 'main', 
                credentialsId: '발급받은credentialsId',
                url: '본인깃허브주소'
            }
        }
        stage('Build'){
            steps{
                
                sh '''./gradlew clean build '''
                
            }
            
        }
    }
}
```
- `sh ''' '''`를 사용하면 쉘스크립트 명령어를 사용할 수 있게 해줍니다.
  - 젠킨스 내에서 해당 파이프라인을 지금 빌드를 누르게 될때마다 위의 clean 후 build 작업이 진행되게 됩니다.

## EC2 프리티어를 사용해서 젠킨스를 이용한 빌드 작업이 먹통일 땐?
#### 1. 주의

사용 중인 EC2 종류에 따라서 스왑 공간도 다르게 부여해준다. 이를 살펴보고 진행하자!

#### 2. 스왑 공간 생성 전 상태 확인
![image](https://github.com/yeomyaloo/jenkins-auto-build/assets/81970382/12f58071-cb71-46c2-ada8-70410138f44c)
- `free -h`

#### 3. dd 명령어로 루트 경로에 스왑 파일 생성

`sudo dd if=/dev/zero of=/swapfile bs=128M count=16` 

> - bs : 블록의 크기
> - count : 블록 수
> - 스왑 파일의 크기 = 블록의 크기 * 블록의 수
> - 즉, 128M * 16 = 2GB이라서 위와 같이 입력한다. 스왑 파일의 크기가 2GB가 되는 것이다.


#### 4. 스왑 파일 읽기 및 쓰기 권한 업데이트 작업
![image](https://github.com/yeomyaloo/jenkins-auto-build/assets/81970382/eca1470c-3216-483b-8ad6-9e3d634fafe1)
- 해당 스왑 파일은 최상단 경로에 생성 된다.
- 해당 최상단 경로로 가서 디렉토리 확인을 하면 swapfile이 생기는 것을 확인 할수 있다.
    - `cd /`
    - `ls`
- `sudo chmod 600 /swapfile` 스왑 파일의 읽기 및 쓰기 권한 업데이트
![image](https://github.com/yeomyaloo/jenkins-auto-build/assets/81970382/01288b08-08f9-4a2e-aaf5-e08835aeeb5d)

#### 5. 스왑 영역을 위해 만든 파일로 설정
- `sudo mkswap /swapfile`

#### 6. 프로시저가 성공적인지 확인
- `sudo swapon -s`
![image](https://github.com/yeomyaloo/jenkins-auto-build/assets/81970382/5f483045-dd0f-42c2-bbda-cee26f2e24ca)

### 7. /etc/fstab 파일을 편집 후 부팅 시 스왑 파일이 시작되게 설정
- `sudo vi /etc/fstab`
    - `/swapfile swap swap defaults 0 0` : 텍스트 편집 시 `i` 혹은 `insert` 키를 누른 뒤 문구를 넣을 수 있게 하고 해당 문구 넣고 저장!
    - Esc → :wq! → Enter
![image](https://github.com/yeomyaloo/jenkins-auto-build/assets/81970382/988b5b4d-efc7-4258-997c-692ec74007fa)

#### 8. 스왑공간이 할당되었는지 확인하기
![image](https://github.com/yeomyaloo/jenkins-auto-build/assets/81970382/8270733b-6966-457d-9a06-579fc5943719)

- `free -h`
- 2G로 할당되었음을 확인할 수 있다.
  
