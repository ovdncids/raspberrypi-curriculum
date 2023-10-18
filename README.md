# 라즈베리파이 4B

## 설치
* [Raspberry Pi Imager](https://www.raspberrypi.com/software)
```sh
운영체제: Raspberry Pi OS (64-bit)
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

# 설정
sudo nano /boot/config.txt

## HDMI 모니터 안켜질 경우 (https://rasino.tistory.com/287)
hdmi_group=2
hdmi_mode=82

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
```

## 서비스
```sh
# 실행중인 서비스 보기
sudo service --status-all
# 서비스 상태
systemctl status fio.service
# 서비스 상태인지 아닌지만 보기
systemctl is-active fio.service
# 서비스 시작
systemctl start fio.service
# 서비스 종료
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

## Utils
```sh
# 용량 (https://power-of-optimism.tistory.com/143)
## 디렉토리별 용량 
df -h
## 전체 용량
df .
```
