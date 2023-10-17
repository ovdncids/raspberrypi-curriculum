# 라즈베리파이 4B

## 설치
* [Raspberry Pi Imager](https://www.raspberrypi.com/software)
```sh
운영체제: Raspberry Pi OS (32-bit)
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

# 설정 (특별히 바꿀것 없음)
sudo nano /boot/config.txt

# VNC
sudo raspi-config
Display Options > VNC Resolution > 1920x1080
Interface Options > VNC > Would you like the VNC server > Yes

sudo reboot
# 재부팅 후에 상단바에 VNC 아이콘이 있어야 Server로 동작한다.
```

## VNC Client
* https://www.realvnc.com/en/connect/download/viewer/macos  
