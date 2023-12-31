# Jenkins

## 설치
* https://pkg.jenkins.io/debian-stable
* https://pimylifeup.com/jenkins-raspberry-pi
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
sudo apt install fontconfig (선택 사항)
sudo apt install jenkins
## invoke-rc.d: initscript jenkins, action "start" failed. (Jenkins 서버 실행 오류 나올 수 있음)
systemctl is-active jenkins
## Failed to connect to bus: Host is down (sudo apt install systemctl)
## `service jenkins start`를 사용하면 `service jenkins stop`이 되지 않을 수 있다.

# Jenkins 접속
http://아이피:8080
## 로딩 페이지에서 5분동안 못 넘어가는 경우
## jenkins 계정으로 jenkins.war 파일을 실행해서 5분안에 Unlock Jenkins 페이지까지 이동 되는지 확인
systemctl stop jenkins
sudo su - jenkins -c "/usr/bin/java -Djava.awt.headless=true -jar /usr/share/java/jenkins.war --webroot=/var/cache/jenkins/war --httpPort=8080"

## 서비스의 Timeout 확인: 기본 90초
sudo systemctl show jenkins.service -p TimeoutStopUSec
### 90초 안에 jenkins.war 파일을 로드하지 못 해서 systemctl가 계속 90초마다 새로 시작 시킴 (900초로 수정)
sudo nano /usr/lib/systemd/system/jenkins.service
TimeoutStartSec=900
sudo reboot

# Unlock Jenkins
sudo cat /var/lib/jenkins/secrets/initialAdminPassword

# Customize Jenkins (오래 걸림)
Install suggested plugins

# Create First Admin User (계정 생성)

# Instance Configuration
Not now
```

### 로그인 비밀번호를 잊어버린 경우
* https://chati.tistory.com/23
```sh
nano /var/lib/jenkins/config.xml

<useSecurity>false</useSecurity>

sudo service jenkins restart
```

### java.nio.charset.UnmappableCharacterException
```sh
# Execute shell 안에 한글이 들어 갔는지 확인

# 서비스를 사용하지 않는 방법 (-Dfile.encoding=UTF-8)
sudo su - jenkins -c "/usr/bin/java -Djava.awt.headless=true -Dfile.encoding=UTF-8 -jar /usr/share/java/jenkins.war --webroot=/var/cache/jenkins/war --httpPort=8080"

# 서비스를 사용하는 방법
nano /usr/lib/systemd/system/jenkins.service
Environment="JAVA_OPTS=-Djava.awt.headless=true -Dfile.encoding=UTF-8"
sudo apt install systemctl
systemctl stop jenkins
systemctl start jenkins
## service jenkins start를 사용하면 `/usr/lib/systemd/system/jenkins.service` 설정이 적용 안될 수 있다.
## Jenkins > Manage Jenkins > Status Information > System Properties > file.encoding > UTF-8 적용 되었는지 확인
```
<!--
systemctl daemon-reload
systemctl restart jenkins.service
* systemctl daemon-reload Failed to connect to bus: Host is down
* https://wiki.terzeron.com/OS_%EC%9D%BC%EB%B0%98_%EC%8B%9C%EC%8A%A4%ED%85%9C/Docker/container%EC%97%90%EC%84%9C_systemctl_%EB%AA%85%EB%A0%B9_%EC%82%AC%EC%9A%A9%EC%8B%9C_Failed_to_get_D-Bus_connection_Operation_not_permitted_%EC%97%90%EB%9F%AC_%ED%95%B4%EA%B2%B0%ED%95%98%EA%B8%B0#google_vignette
-->

## Linux - Execute shell
* Project 생성 후 > Configure > Build Steps > Execute shell > Command
```sh
# 3000 port 확인
netstat -tnlp | grep 3000 || echo "3000 port is not running."
# 3000 port 종료
fuser -k 3000/tcp || echo "Can not turn off 3000 port"
## fuser: command not found (sudo apt install psmisc)

# 소스 복사, 빌드, 실행
rm -fr /var/lib/jenkins/build/next-study-will-delete/source
git clone --branch main https://github.com/ovdncids/next-study-will-delete.git /var/lib/jenkins/build/next-study-will-delete/source
cd /var/lib/jenkins/build/next-study-will-delete/source
npm install
npm run build
npm run start &
```
```sh
# Jenkins에서는 Execute shell 끝나면 child process를 전부 kill 시키므로 BUILD_ID를 사용해서 kill을 회피한다.
# nohup 명령을 쓰면 nohup.out 파일에 출력 내역이 저장 됨
# &로 백그라운드 작업으로 전환(jobs에 존재) 하고, 터미널을 종료 하면 해당 백그라운드 명령은 죽이지 않는다.
# 2>&1는 1 = 표준 출력, 2 = 표준 에러, 2개 모두 log.out 파일로 받겠다는 뜻이다.
BUILD_ID=leaveNohup nohup npm run start > ../log.out 2>&1 &

# nohup을 사용하지 않고, 1 = 표준 출력, 2 = 표준 에러, 동시에 다른 파일로 사용 가능하다.
BUILD_ID=leaveNpm npm run start 1> ../log.out 2> ../err.out &
```

### Startup - NVM
* [리눅스 부팅 과정](https://eine.tistory.com/entry/%EB%A6%AC%EB%88%85%EC%8A%A4-%EB%B6%80%ED%8C%85%EA%B3%BC%EC%A0%95%EA%B3%BC-%EB%B6%80%ED%8C%85%EC%8B%9C-%EB%A7%88%EB%8B%A4-%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8-%EC%8B%A4%ED%96%89Startup-Script)
* `jenkins 계정`의 루트는 `/var/lib/jenkins`이고 로그인 하여도 `~/.bashrc 파일`을 읽지 않는다.
* `.sh 파일`에서는 `source` 명령을 사용할 수 없으므로 `#!/usr/bin/env bash`를 넣어야 한다.
* `ls /home/pi/.nvm` 권한이 없다고 나오면 `chmod 755 /home/pi`
```sh
sudo su - jenkins
nano ~/.bashrc

# NVM
export NVM_DIR="/home/pi/.nvm"
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"  # This loads nvm
[ -s "$NVM_DIR/bash_completion" ] && \. "$NVM_DIR/bash_completion"  # This loads nvm bash_completion
```
```sh
nano /var/lib/jenkins/build/next-study-will-delete/startup.sh

#!/usr/bin/env bash

source ~/.bashrc
nvm use 20.8.1
npm -v
cd /var/lib/jenkins/build/next-study-will-delete/source
npm run start 1> ../log.out 2> ../err.out &
```
```sh
chmod 755 ./startup.sh
```
```sh
# 부팅시 실행하는 스크립트 파일 (pi 계정으로 진행)
sudo nano /etc/rc.local
# Jenkins
sudo su - jenkins -c './startup.sh'
## jenkins 계정으로 /var/lib/jenkins/startup.sh 파일 실행
## /etc/rc.local 아래 exit 0이 있으므로 상단부에 추가

sudo reboot
```

* `Jenkins - Execute shell`에서 또한 `source` 명령을 사용할 수 없으므로 `#!/usr/bin/env bash`를 넣어야 한다.
```sh
#!/usr/bin/env bash
source ~/.bashrc
nvm use 20.8.1
```

### 백그라운드 작업
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

# 현재 터미널의 백그라운드에서 전역 백그라운드로 이동 (jobs에서 없어짐)
# 따라서 현재 터미널이 종료 되어도 disown된 백그라운드 작업은 종료 되지 않는다.
disown %1
jobs -l

# 첫번째 백그라운드 작업 멈춤
kill -STOP %1

# 첫번째 백그라운드 작업 종료
kill -9 %1
```

## Windows - Execute Windows batch command
* [This account either does not have the privilege to logon as a service](https://stackoverflow.com/questions/63410442/jenkins-installation-windows-10-service-logon-credentials)
```cmd
REM whoami 대신 cd를 사용한다.
cd
echo &path&
REM path에 nodejs 경로 추가
REM set path=%path%;C:\Program Files\nodejs\;

REM `npm, npx`를 실행하면 다음줄을 실행하지않고 종료되므로 `call`을 사용해야 한다.
call npm install -g kill-port
call npx kill-port 3000

rmdir /s /q next-study-will-delete
git clone --branch main https://github.com/ovdncids/next-study-will-delete.git
cd next-study-will-delete

call npm install
call npm run build

REM `start cmd /k` Jenkins를 종료하기 위해 새로운 cmd로 `npm run start` 실행
start cmd /k "npm run start 1> log.out 2> err.out"
```
* 한글이 포함되면 `Input length = 1` 오류 발생할 수 있다.
