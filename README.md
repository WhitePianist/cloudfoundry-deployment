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
- [bosh-lite Download & VM run](#bosh-lite_Download_&_VM_run)  
- [add Route](#add_Route)  
- [BOSH CLI](#BOSH_CLI)  
- [CF CLI](#CF_CLI)  
- [Spiff](#Spiff)  

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

#### bosh-lite Download & VM run
```
cd ~/workspace
git clone https://github.com/cloudfoundry/bosh-lite
cd bosh-lite

vagrant up --provider=virtualbox
```
##### Error1 - Secure Boot
에러내용:
>The provider 'virtualbox' that was requested to back the machine
>'default' is reporting that it isn't usable on this system. The
>reason is shown below:
>
>VirtualBox is complaining that the kernel module is not loaded. Please
>run `VBoxManage --version` or open the VirtualBox GUI to see the error
>message which should contain instructions on how to fix this error.

조치방법: os의 secure boot를 disable 해야 한다.

##### Error2 - Network
에러내용:
>The specified host network collides with a non-hostonly network!
>This will cause your specified IP to be inaccessible. Please change
>the IP or name of your host only network so that it no longer matches that of
>a bridged or non-hostonly network.

조치방법: Vagrantfile 수정
override.vm.network :private_network  << 주석 제거 후 저장. 다시 vagrant up

#### add Route
```
cd ~/workspace/bosh-lite/bin
add-route
```

#### BOSH CLI
```
gem install bosh_cli --no-ri --no-rdoc
bosh target https://192.168.50.4:25555
```

#### CF CLI
```
curl -L "https://cli.run.pivotal.io/stable?release=linux64-binary&source=github" | tar xvz
chmod +x cf
sudo mv cf /usr/local/bin
```

#### Spiff
```
wget https://github.com/cloudfoundry-incubator/spiff/releases/download/v1.0.8/spiff_linux_amd64.zip
unzip spiff_linux_amd64.zip; rm –f spiff_linux_amd64.zip
sudo cp spiff /usr/local/bin   -> sudo mv spiff /usr/local/bin 
```


### Cloud Foundry Deploy
```
cd ~/workspace
git clone https://github.com/cloudfoundry/cf-release
cd cf-release
```
