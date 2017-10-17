# cloudfoundry-deployment

## bosh-lite

### Pre-Reqs
```
sudo apt-get update
```
- [RVM](#RVM)  
- [Go](#Go)  
- [VirtualBox](#VirtualBox)  
- [Vagrant](#Vagrant)  
- [Git](#Git)  
- [Curl](#Curl)  

#### Go
```
wget https://storage.googleapis.com/golang/go1.8.3.linux-amd64.tar.gz
sudo tar -C /usr/local -xzf go1.8.3.linux-amd64.tar.gz
echo 'export GOROOT=/usr/local/go' >> ~/.bash_profile
echo 'export PATH=$PATH:$GOROOT/bin' >> ~/.bash_profile

. ~/.bash_profile
```

#### RVM
```
gpg --keyserver hkp://keys.gnupg.net --recv-keys 409B6B1796C275462A1703113804BB82D39DC0E3
\curl -sSL https://get.rvm.io | bash -s stable --ruby
source ~/.rvm/scripts/rvm

rvm install ruby-2.3.0
```

#### VirtualBox
```
sudo apt-get install libqt5x11extras5 libsdl1.2debian
sudo apt-get install linux-headers-$(uname -r) build-essential dkms
```
[VirtualBox 홈페이지](https://www.virtualbox.org)에서 설치파일 다운로드 후 설치
```
sudo dpkg -i virtualbox-***.deb
```

#### Vagrant
[Vagrant 다운로드 페이지](https://www.vagrantup.com/downloads.html)에서 설치파일 다운로드 후 설치
```
sudo dpkg -i vagrant_***.deb
```

#### Git
```
sudo apt-get install git
```

#### Curl
```
sudo apt-get install curl
```
