# cloudfoundry-deployment

## bosh-lite

### Pre-Reqs
```
sudo apt-get update
```
- [Go](#Go)  
- [RVM](#RVM)  
- [Git](#Git)  
- [Curl](#Curl)  
- [VirtualBox](#VirtualBox)  
- [Vagrant](#Vagrant)  

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

#### Git
```
sudo apt-get install git
```

#### Curl
```
sudo apt-get install curl
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


### Cloud Foundry Deploy

#### bosh-lite Download & VM run
```
cd ~/workspace
git clone https://github.com/cloudfoundry/bosh-lite
cd bosh-lite

vagrant up --provider=virtualbox
```

>실행 시 아래와 같은 에러가 발생하면 os의 secure boot를 disable 해야 한다.

>The provider 'virtualbox' that was requested to back the machine
>'default' is reporting that it isn't usable on this system. The
>reason is shown below:
>
>VirtualBox is complaining that the kernel module is not loaded. Please
>run `VBoxManage --version` or open the VirtualBox GUI to see the error
>message which should contain instructions on how to fix this error.

>아래와 같은 에러가 발생하면 Vagrantfile 수정
>override.vm.network :private_network  << 주석 제거 후 저장. 다시 vagrant up

>The specified host network collides with a non-hostonly network!
>This will cause your specified IP to be inaccessible. Please change
>the IP or name of your host only network so that it no longer matches that of
>a bridged or non-hostonly network.

#### 
