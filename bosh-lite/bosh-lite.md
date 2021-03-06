# cloudfoundry-deployment

## bosh-lite

### Pre-Reqs
```
sudo apt-get update
```
- [Go](#go)  
- [RVM](#rvm)  
- [Git](#git)  
- [Curl](#curl)  
- [VirtualBox](#virtualbox)  
- [Vagrant](#vagrant)  
- [bosh-lite Download & VM run](#bosh-lite-download--vm-run)  
- [Route](#route)  
- [BOSH CLI](#bosh-cli)  
- [CF CLI](#cf-cli)  
- [Spiff](#spiff)  

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
gem install bundle --no-document
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
- 에러내용:
>The provider 'virtualbox' that was requested to back the machine
>'default' is reporting that it isn't usable on this system. The
>reason is shown below:
>
>VirtualBox is complaining that the kernel module is not loaded. Please
>run `VBoxManage --version` or open the VirtualBox GUI to see the error
>message which should contain instructions on how to fix this error.

- 조치방법: os의 secure boot를 disable 해야 한다.

##### Error2 - Network
- 에러내용:
>The specified host network collides with a non-hostonly network!
>This will cause your specified IP to be inaccessible. Please change
>the IP or name of your host only network so that it no longer matches that of
>a bridged or non-hostonly network.

- 조치방법: 
1. Vagrantfile 수정  
override.vm.network :private_network  << 주석 제거, 필요시 ip 변경 후 저장
1. bin/add-route << Vagrantfile에서 ip 변경 시 이 파일에서도 동일하게 변경

#### Route
```
cd ~/workspace/bosh-lite/bin
add-route
```

#### BOSH CLI
```
gem install bosh_cli --no-ri --no-rdoc
bosh target https://192.168.50.4:25555
(admin / admin)
```

#### CF CLI
```
curl -L "https://cli.run.pivotal.io/stable?release=linux64-binary&source=github" | tar xvz
chmod +x cf
sudo mv cf /usr/local/bin
```

#### Spiff
```
wget https://github.com/cloudfoundry-incubator/spiff/releases/download/v1.0.7/spiff_linux_amd64.zip
unzip spiff_linux_amd64.zip; rm -f spiff_linux_amd64.zip
sudo mv spiff /usr/local/bin
```

### BOSH Stemcell
#### Download & Upload Stemcell
```
wget --content-disposition https://bosh.io/d/stemcells/bosh-warden-boshlite-ubuntu-trusty-go_agent?v=3445.2
bosh upload stemcell bosh-stemcell-3445.2-warden-boshlite-ubuntu-trusty-go_agent.tgz
```
or
```
bosh upload stemcell https://bosh.io/d/stemcells/bosh-warden-boshlite-ubuntu-trusty-go_agent?v=3445.2
```

### Garden RunC
```
cd ~/workspace
git clone https://github.com/cloudfoundry/garden-runc-release
cd garden-runc-release
git checkout v1.9.0
bosh -n upload release releases/garden-runc/garden-runc-1.9.0.yml
```
or
```
bosh upload release https://bosh.io/d/github.com/cloudfoundry/garden-runc-release?v=1.9.0
```

### cflinuxfs2
```
cd ~/workspace
git clone https://github.com/cloudfoundry/cflinuxfs2-release
cd cflinuxfs2-release
git checkout v1.146.0
bosh -n upload release releases/cflinuxfs2/cflinuxfs2-1.146.0.yml
```
or
```
bosh upload release https://bosh.io/d/github.com/cloudfoundry/cflinuxfs2-release?v=1.146.0
```

### Cloud Foundry Deploy
#### Specific Version Deploy(v272)
```
cd ~/workspace
git clone https://github.com/cloudfoundry/cf-release
cd cf-release
git checkout v272
./scripts/generate-bosh-lite-dev-manifest 2>/dev/null
./scripts/generate-bosh-lite-dev-manifest
bosh -n upload release releases/cf-272.yml
bosh -n deploy

bosh vms
```
#### Custom Deploy
```
cd ~/workspace
git clone https://github.com/cloudfoundry/cf-release
cd cf-release
git checkout v272
./scripts/update
bosh -n create release --force
bosh -n upload release
bosh -n deploy

bosh vms
```

### Diego Deploy
#### Specific Version Deploy(1.25.1)
```
cd ~/workspace
git clone https://github.com/cloudfoundry/diego-release
cd diego-release
git checkout v1.25.1
./scripts/generate-bosh-lite-manifests
bosh deployment bosh-lite/deployments/diego.yml
bosh -n upload release releases/diego-1.25.1.yml
(or bosh upload release https://bosh.io/d/github.com/cloudfoundry/diego-release?v=1.25.1)
bosh -n deploy

bosh vms
```
#### Custom Deploy
```
cd ~/workspace
git clone https://github.com/cloudfoundry/diego-release
cd diego-release
git checkout v1.26.1
./scripts/update
bosh -n create release --force
bosh -n upload release
bosh -n deploy

bosh vms
```

## App Push Test
### cf target
```
cf api --skip-ssl-validation api.bosh-lite.com
cf auth admin admin
cf create-org org
cf create-space space -o org
cf target -o org -s space
```

### php app push
```
cf enable-feature-flag diego_docker
mkdir -p test-php; cd test-php
echo "<?php phpinfo(); ?>" > index.php
cf push test-php -b php_buildpack

cf apps
```
