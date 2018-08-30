# Ubuntu 16.04 개발환경 구성

- [한글설정](#한글설정)   
- [java 설치](#java-설치)   
- [go 설치](#go-설치)   
- [godep 설치](#godep-설치)   
- [direnv 설치](#direnv-설치)   
- [curl 설치](#curl-설치)   
- [mysql 설치](#mysql-설치)   
- [PostgreSQL](#postgresql)   
- [git 설치](#git-설치)   
- [maven 설치](#maven-설치)   
- [intellij 설치](#intellij-설치)   
- [docker 설치](#docker-설치)   
- [protobuf 설치](#protobuf-설치)   
- [rvm](#rvm)   
- [bosh cli](#bosh-cli)   
- [cf cli](#cf-cli)   
- [uaa cli](#uaa-cli)   
- [PaaSXpert cli](#paasxpert-cli)   
- [virtual box](#virtual-box)   
- [vagrant](#vagrant)   
- [Ubuntu 설정](#ubuntu-설정)   
- [vim](#vim)   
- [nvidia driver 설치](#nvidia-driver-설치)   
- [alarm-clock 설치](#alarm-clock-설치)    
- [Ubuntu 환경변수 및 환경설정](#ubuntu-환경변수-및-환경설정)   


## 한글설정
http://hochulshin.com/ubuntu-1604-hangul/
http://b1ix.net/207


## java 설치
```
sudo add-apt-repository ppa:webupd8team/java
sudo apt-get update
sudo apt-get install oracle-java8-installer
```
설치 중 라이센스 어쩌구 물으면 "yes"  
http://www.webupd8.org/2012/09/install-oracle-java-8-in-ubuntu-via-ppa.html


## go 설치
```
wget https://storage.googleapis.com/golang/go1.8.3.linux-amd64.tar.gz
sudo tar -C /usr/local -xzf go1.8.3.linux-amd64.tar.gz
echo 'export GOROOT=/usr/local/go' >> ~/.bash_profile
echo 'export PATH=$PATH:$GOROOT/bin' >> ~/.bash_profile

. ~/.bash_profile
```


## godep 설치
```
go get github.com/tools/godep
```


## direnv 설치
```
git clone https://github.com/direnv/direnv
cd direnv
sudo make install
```
http://direnv.net/


## curl 설치
```
sudo apt-get install curl
```


## mysql 설치
```
sudo apt-get install mysql-server-5.7 -y
sudo vi /etc/mysql/mysql.conf.d/mysqld.cnf
#bind-address           = 127.0.0.1
sudo service mysql restart

mysql -uroot -ppassword
INSERT INTO mysql.user (host,user,authentication_string, ssl_cipher, x509_issuer, x509_subject) VALUES ('%','root',password('password'),'','','');
GRANT ALL PRIVILEGES ON *.* TO 'root'@'%';
FLUSH PRIVILEGES;

INSERT INTO mysql.user (host,user,authentication_string, ssl_cipher, x509_issuer, x509_subject) VALUES ('%','monasca',password('password'),'','','');
GRANT ALL PRIVILEGES ON *.* TO 'monasca'@'%';
FLUSH PRIVILEGES;
```
- 참고 1. 접속실패 시
https://zetawiki.com/wiki/ERROR_2003_(HY000):_Can%27t_connect_to_MySQL_server_on
- 참고 2. 원격접속 허용
https://zetawiki.com/wiki/MySQL_%EC%9B%90%EA%B2%A9_%EC%A0%91%EC%86%8D_%ED%97%88%EC%9A%A9
- 참고 3. mysql client tool  
sudo apt-get install mysql-workbench -y


## PostgreSQL
- ### 설치
/etc/apt/sources.list.d/pgdg.list 파일을 만들고, 아래 저장소에 대한 행을 입력한다.
```
deb http://apt.postgresql.org/pub/repos/apt/ YOUR_UBUNTU_VERSION_HERE-pgdg main
```
저장소 키를 가져오고 패키지목록을 업데이트한다.
```
wget --quiet -O - https://www.postgresql.org/media/keys/ACCC4CF8.asc | \
  sudo apt-key add -
sudo apt-get update
```
우분투에는 기본적으로 PostgreSQL이 포함되어 있다.
```
sudo apt-get install postgresql
```
원하는버전으로설치하는경우:
```
sudo apt-get install postgresql-9.6
```
설치확인
```
dpkg -l | grep postgres
```
https://www.postgresql.org/download/linux/ubuntu/

- ### 초기설정
다음과 같이 postgres계정으로 접속 시 오류가 발생 할 경우
```
$ psql -U postgres
psql: FATAL:  Peer authentication failed for user "postgres"
```
아래 파일에서 local의 peer를 md5로 수정하고 서비스를 재시작한다.
```
sudo vi /etc/postgresql/9.6/main/pg_hba.conf
```
```
#local   all             postgres                                peer
local   all             postgres                                md5
```
```
$ sudo service postgresql restart
$ psql -U postgres
Password for user postgres:
```
위 패스워드는 postgres

http://blog.secretmuse.net/?p=10


## git 설치
```
sudo apt-get install git -y
```
### git Credential 저장
protocol, host, username, password를 미리 등록해 두어 매번 Credential정보를 입력할 필요없이 Remote와 통신한다.
```
$ git credential-store store
protocol=https
host=github.com
username=khj0651
password=xxxxxxxxx

$ git config --global credential.helper STORE
```


## maven 설치
```
sudo apt-get install maven -y
```


## intellij 설치
```
intellij home > download > previous version
ideaIU-2016.1.4.tar.gz
tar -zxvf ideaIU-2016.1.4.tar.gz
```
실행 시 serial key 입력
```
User name: 주) 크로센트
===== LICENSE BEGIN =====
1070754-27052015
000013ZdVjOOwiapPOftInl7o0i!8N
zyWFidd5desBAqHmGWtePNdezutA6U
L4Hh!KYHwRZEx20eDOLtCOs9mlRSBS
===== LICENSE END =====
```
입력하면 convert하라고 나오는데
```
first name: 주)
lastname: 크로센트
email: hjinkim@crossent.com
```


## docker 설치
```
sudo apt-get update
sudo apt-key adv --keyserver hkp://p80.pool.sks-keyservers.net:80 --recv-keys 58118E89F3A912897C070ADBF76221572C52609D
echo "deb https://apt.dockerproject.org/repo ubuntu-xenial main" | sudo tee /etc/apt/sources.list.d/docker.list
sudo apt-get update
apt-cache policy docker-engine
sudo apt-get install -y docker-engine
sudo systemctl status docker
sudo usermod -aG docker $(whoami)
sudo reboot

docker run -p 6379:6379 --name redis -d redis

docker run -p 3306:3306 --name mysql -e MYSQL_ROOT_PASSWORD=password -d mysql --character-set-server=utf8 --collation-server=utf8_general_ci

docker run -it --link mysql:mysql --rm mysql sh -c 'exec mysql -h"$MYSQL_PORT_3306_TCP_ADDR" -P"$MYSQL_PORT_3306_TCP_PORT" -uroot -p"$MYSQL_ENV_MYSQL_ROOT_PASSWORD" -e "create database PaasXpertPortal"'

docker run -it --link mysql:mysql --rm mysql sh -c 'exec mysql -h"$MYSQL_PORT_3306_TCP_ADDR" -P"$MYSQL_PORT_3306_TCP_PORT" -uroot -p"$MYSQL_ENV_MYSQL_ROOT_PASSWORD"'
```


## protobuf 설치
shell파일 실행
https://github.com/cloudfoundry/cf-java-client/blob/v1.1.4.RELEASE/bin/install-protoc-osx.sh


## rvm
```
gpg --keyserver hkp://keys.gnupg.net --recv-keys 409B6B1796C275462A1703113804BB82D39DC0E3
\curl -sSL https://get.rvm.io | bash -s stable --ruby
source ~/.rvm/scripts/rvm

rvm install ruby-2.3.0
```
https://rvm.io/rvm/install


## bosh cli
```
gem install bosh_cli --no-ri --no-rdoc
bosh target https://115.68.151.183:25555
admin / admin
```
https://bosh.io/docs/bosh-cli.html


## cf cli
아래 링크에서 deb파일 다운로드 받아 직접 설치
https://github.com/cloudfoundry/cli


## uaa cli
```
gem install cf-uaac
```


## PaaSXpert cli
https://github.com/CrossentCloud/PaaSXpert-Core
sudo cp cf /usr/bin/


## virtual box
sudo apt-get install linux-headers-$(uname -r) build-essential dkms
sudo apt-get install libqt5x11extras5 libsdl1.2debian
https://www.virtualbox.org/wiki/Linux_Downloads 가서 파일받고
sudo dpkg -i virtualbox-****.deb

"네트워크 - 호스트 전용 네트워크" 생성 시 문제 해결
https://askubuntu.com/questions/900118/vboxdrv-sh-failed-modprobe-vboxdrv-failed-please-use-dmesg-to-find-out-why

## vagrant



## Ubuntu 설정
- 한/영 키 클릭 시 대시홈 열리지 않도록 설정
시스템 설정 > 키보드 > 바로 가기 > 실행 아이콘 > 허드를 표시할 키
선택하고 Backspace 눌러 사용 안 함으로 변경
http://devmonster.tistory.com/48


## vim
```
sudo apt-get install vim
```


## nvidia driver 설치
Nvidia 그래픽카드인 경우 필요시 설치
```
sudo apt-get purge nvidia-*
sudo add-apt-repository ppa:graphics-drivers/ppa and then sudo apt-get update.
sudo apt-get install nvidia-364.
sudo reboot
```
http://askubuntu.com/questions/760934/graphics-issues-after-installing-ubuntu-16-04-with-nvidia-graphics  
참고(http://blog.daum.net/bagjunggyu/66)



## alarm-clock 설치
Ubuntu Software > "alarm"으로 검색 > "Alarm Clock" Install
#### 요일별 반복기능 제공
"Time"에 반복하려는 시간 입력하고(14:00:00) "Repeat"에 원하는 요일 선택
#### 알람 시 명령 실행
"Start Application" 선택 후 "Command"에 아래 명령 입력  
예) 크롬 브라우저 열고 URL호출 명령실행  
```
google-chrome-stable https://docs.google.com/spreadsheets/d/1Ls9gZ9xfcT7C-jr3xDpQ8TOKDNcXvjyQGU_vrtmW-Wo/edit#gid=0
```



## Ubuntu 환경변수 및 환경설정
```
export GOROOT=/usr/local/go
export PATH=$PATH:$GOROOT/bin

# Add RVM to PATH for scripting. Make sure this is the last PATH variable change.
export PATH="$PATH:$HOME/.rvm/bin"

# bosh2
export BOSH_CLIENT=admin
export BOSH_CLIENT_SECRET=`bosh int /home/hyojin/deployments/vbox/creds.yml --path /admin_password`

# cf admin password
export CF_ADMIN_PASSWORD=$(bosh int /home/hyojin/workspace/cf-deployment/deployment-vars.yml --path /cf_admin_password)
export UAA_ADMIN_CLIENT_SECRET=$(bosh int /home/hyojin/workspace/cf-deployment/deployment-vars.yml --path /uaa_admin_client_secret)


[[ -s "$HOME/.rvm/scripts/rvm" ]] && source "$HOME/.rvm/scripts/rvm" # Load RVM into a shell session *as a function*

alias goweb='cd /home/hyojin/workspace/MsXpert/microservice/src/crossent/micro/studio/web'
alias goms='cd /home/hyojin/workspace/MsXpert'
alias gomss='cd /home/hyojin/workspace/MsXpert-spring-cloud'
```
