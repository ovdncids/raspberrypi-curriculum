# 라즈베리파이 4B

## 설치
* [Raspberry Pi Imager](https://www.raspberrypi.com/software)
```sh
운영체제: Raspberry Pi OS (LEGACY)
저장소: SD카드 선택

# 설정
hostname 설정: raspberrypi
SSH 사용
사용자 이름: pi
Wifi: 자동 입력
Wifi 국가: GB
시간대: Asia/Seoul
키보드 레이아웃: us

# 쓰기
모든 데이터가 지워집니다. 예
```

## 원격 접속 (putty 또는 ssh)
```sh
# 접속
ssh pi@아이피

## IT IS POSSIBLE THAT SOMEONE IS DOING SOMETHING NASTY! (https://cpuu.postype.com/post/30065)
ssh-keygen -R 아이피

# 설정
sudo nano /boot/config.txt

## HDMI 최소 해상도
hdmi_save=1

## HDMI 모니터 안켜질 경우 (https://rasino.tistory.com/287)
hdmi_group=2
hdmi_mode=82
### 82 = 1920x1080 (1080p)
### 85 = 1280x700 (720p)

# VNC
sudo raspi-config
Display Options > VNC Resolution > 1920x1080
Interface Options > VNC > Would you like the VNC server > Yes

sudo reboot
# 재부팅 후에 상단바에 VNC 아이콘이 있어야 Server로 동작한다.
```

### VNC Server 32-bit RasPiOS issue
* https://github.com/raspberrypi/bookworm-feedback/issues/41

### VNC Client
* https://www.realvnc.com/en/connect/download/viewer/macos  

## 한글
* https://velog.io/@thdusdl4767/Raspberry-Pi-Raspberry-Pi-%ED%95%9C%EA%B8%80-%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%A8-%EC%84%A4%EC%B9%98
```sh
sudo apt install fonts-nanum
sudo apt install ibus-hangul

Preferences > Raspberry Pi Configuration > Localisation > Set Locale... > ko (Korean), Character Set: UTF-8
리부팅

ls –ld .config/ibus
sudo rm –r .config/ibus
Preferences > IBus 기본 설정 > 바로 가기 키 > <Shift>space
                           > 입력 방식 > 한국어 - Hangul
상단 Task > 한글 - Hangul > 한글 상태 체크
```

## 서비스
```sh
# 실행중인 서비스 보기
sudo service --status-all
# 서비스 상태
systemctl status fio.service
# 서비스 상태인지 아닌지만 보기
systemctl is-active fio.service
# 서비스 시작 (1회성)
systemctl start fio.service
# 서비스 종료 (1회성)
systemctl stop fio.service
```

## Anydesk
* https://anydesk.com/en/downloads/raspberry-pi
* https://remnant24c.tistory.com/330
* http://www.acronet.kr/29221
```sh
# 설치
wget https://download.anydesk.com/rpi/anydesk_6.1.1-1_armhf.deb
sudo dpkg -i anydesk_6.1.1-1_armhf.deb
# 리스트
sudo dpkg -l
# 삭제
sudo dpkg -r anydesk
```

## RDP (원격 데스트톱 연결)
```sh
sudo apt install xrdp
```

## Utils
```sh
# 용량 (https://power-of-optimism.tistory.com/143)
## 디렉토리별 용량 
df -h
## 전체 용량
df .
```

## Node.js
* https://it-jm.tistory.com/19
```sh
sudo curl -sL https://deb.nodesource.com/setup_16.x | sudo -E bash -
sudo apt-get install nodejs
리부팅
node -v
```

## MariaDB
* https://velog.io/@dogakday/%EB%9D%BC%EC%A6%88%EB%B2%A0%EB%A6%AC%ED%8C%8C%EC%9D%B4-Maria-DB-%EC%84%A4%EC%B9%98
```sh
sudo apt install mariadb-server
systemctl is-active mysql
sudo mysql -u root
```

## Java
* https://backendcode.tistory.com/262
```sh
# 설치
sudo apt install openjdk-8-jdk
# or 
sudo apt install openjdk-11-jdk
java -version

sudo nano ~/.bashrc
# $JAVA_HOME
export JAVA_HOME=$(dirname $(dirname $(readlink -f $(which java))))
export PATH=$PATH:$JAVA_HOME/bin

source ~/.bashrc
echo $JAVA_HOME
```

## Jenkins
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

### Execute shell
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
# exit 명령를 사용하면 "npm run start &" 백그라운드 명령까지 종료가 된다.
exit

# nohup 명령을 쓰면 nohup.out 파일에 출력 내역이 저장 되고
# 일반 터미널에서는 터미널 종료 후에도 해당 백그라운드 명령이 살아 있는다.
nohup npm run start &
```
* 백그라운드 작업
```sh
# 백그라운드로 10000초 sleep
sleep 10000 &

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

# 현재 터미널의 백그라운드에서 전역 백그라운드로 이동 (nohup 명령과 비슷)
# 따라서 현재 터미널이 종료 되어도 disown된 백그라운드 작업은 종료 되지 않는다.
disown %1
jobs -l

# 첫번째 백그라운드 작업 종료
kill -9 %1
```
