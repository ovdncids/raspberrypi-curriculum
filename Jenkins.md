# Jenkins

## 설치
* https://pkg.jenkins.io/debian-stable
* https://pimylifeup.com/jenkins-raspberry-pi/
```sh
# Jenkins key 생성
sudo wget -O /usr/share/keyrings/jenkins-keyring.asc \
    https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key

# Jenkins repository
echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \
    https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
    /etc/apt/sources.list.d/jenkins.list > /dev/null

# Jenkins 설치
sudo apt update
sudo apt install fontconfig
sudo apt install jenkins

# invoke-rc.d: initscript jenkins, action "start" failed. (Jenkins 서버 실행 오류 나올 수 있음)
systemctl is-active jenkins

# Jenkins 접속
http://아이피:8080

# Unlock Jenkins
sudo cat /var/lib/jenkins/secrets/initialAdminPassword

# Customize Jenkins (오래 걸림)
Install suggested plugins

# Create First Admin User (계정 생성)

# Instance Configuration
Not now
```

## Execute shell
* Project 생성 후 > Configure > Build Steps > Execute shell > Command
```sh
# 3000 port 확인
netstat -tnlp | grep 3000 || echo "3000 port is not running."
# 3000 port 종료
fuser -k 3000/tcp || echo "Can not turn off 3000 port"

# 소스 복사, 빌드, 실행
rm -fr /var/lib/jenkins/build/next-study-will-delete
git clone https://github.com/ovdncids/next-study-will-delete.git /var/lib/jenkins/build/next-study-will-delete
cd /var/lib/jenkins/build/next-study-will-delete
npm install
npm run build
npm run start &
```
```sh
# Jenkins에서는 Execute shell 끝나면 child process를 전부 kill 시키므로 BUILD_ID를 사용해서 kill을 회피한다.
# nohup 명령을 쓰면 nohup.out 파일에 출력 내역이 저장 됨
# &로 백그라운드 작업으로 전환(jobs에 존재) 하고, 터미널을 종료 하면 해당 백그라운드 명령은 죽이지 않는다.
# 2>&1는 1 = 표준 출력, 2 = 표준 에러, 2개 모두 log.out 파일로 받겠다는 뜻이다.
BUILD_ID=leaveNohup nohup npm run start > log.out 2>&1 &

# nohup을 사용하지 않고, 1 = 표준 출력, 2 = 표준 에러, 동시에 다른 파일로 사용 가능하다.
BUILD_ID=leaveNpm npm run start 1> log.out 2> err.out &
```
* 백그라운드 작업
```sh
# 백그라운드로 10000초 sleep
sleep 10000 &

# 해당 백그라운드 찾기
ps -ef | grep "sleep 10000"

# 백그라운드 작업 리스트
jobs -l

# 첫번째 백그라운드 작업으로 돌아가기
fg %1
## Ctrl + z: 백그라운드 작업 멈춤

# 첫번째 백그라운드 작업이 멈추었는지 확인
jobs -l

# 첫번째 백그라운드 작업 다시 시작
bg %1
jobs -l

# 현재 터미널의 백그라운드에서 전역 백그라운드로 이동 (jobs에서 없어짐)
# 따라서 현재 터미널이 종료 되어도 disown된 백그라운드 작업은 종료 되지 않는다.
disown %1
jobs -l

# 첫번째 백그라운드 작업 멈춤
kill -STOP %1

# 첫번째 백그라운드 작업 종료
kill -9 %1
```

## Execute Windows batch command
```cmd
REM whoami 대신 cd를 사용한다.
cd
echo &path&
REM path에 nodejs 경로 추가
REM set path=%path%;C:\Program Files\nodejs\;

REM `npm, npx`를 실행하면 다음줄을 실행하지않고 종료되므로 `call`을 사용해야 한다.
call npx kill-port 3000

rmdir /s /q next-study-will-delete
git clone https://github.com/ovdncids/next-study-will-delete.git
cd next-study-will-delete

call npm install
call npm run build

REM `start cmd /k` Jenkins를 종료하기 위해 새로운 cmd로 `npm run start` 실행
start cmd /k "npm run start 1> log.out 2> err.out"
```
