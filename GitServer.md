# Git Server
## 설치
```sh
sudo apt install git-core
# 사용자 git 추가
sudo useradd -s /bin/bash -m git
sudo passwd git
su git
```

## Repository 만들기
```sh
mkdir /home/git/Repositories
mkdir /home/git/Repositories/repository-a
cd /home/git/Repositories/repository-a
git init --bare
```

## Project 클론 (편함, 원격 클론 가능)
```sh
mkdir /home/git/Projects
cd /home/git/Projects
git clone ssh://git@localhost:/home/git/Repositories/repository-a
```

## Project에 remote 추가
```sh
cd /home/git/Projects
mkdir repository-a
cd repository-a
git init
git remote add origin git@localhost:/home/git/Repositories/repository-a
echo "# README" >> README.md
git add .
git commit -m "README.md"
git push origin master
```
* `git clone ssh://git@[ip]:[포트번호]/home/git/Repositories/repository-a`

## Push, Pull 할때 비밀번호 묻지 않기
* https://hackernoon.com/a-guide-on-how-to-host-your-own-git-server-with-raspberry-pi
* https://www.lainyzine.com/ko/article/handling-ssh-permission-denied-errors
```sh
# SSH 키 파일 만들기
ssh-keygen
## Enter file in which to save the key (/Users/사용자/.ssh/id_rsa): /Users/사용자/.ssh/git_rsa

# SSH 키 파일에 연결 정보 넣기
ssh-copy-id -i ~/.ssh/git_rsa.pub -p 포트 git@아이피
## 로그인

# 셀에 등록
ssh-agent zsh && ssh-add ~/.ssh/git_rsa
## 셸이 새로 열린다.
exit
git pull
```
