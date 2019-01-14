# bosh-lite (CLI v2)

- [Pre-Reqs](#pre-reqs)   
- [Install CLI v2](#install-cli-v2)   
- [Install Director VM](#install-director-vm)   
- [Alias and log into the Director](#alias-and-log-into-the-director)   
- [Local Route](#local-route)   
- [Update cloud config](#update-cloud-config)   
- [Upload stemcell](#upload-stemcell)   
- [Deploy CF](#deploy-cf)
- [Release Modify](#release-modify)
- [Commands](#commands)

## Pre-Reqs
```
$ sudo apt-get update
```
Install [VirtualBox](https://www.virtualbox.org/wiki/Downloads)


## Install CLI v2
Install [Bosh CLI](https://github.com/cloudfoundry/bosh-cli/releases)
```
$ chmod +x ~/Downloads/bosh-cli-*
$ sudo mv ~/Downloads/bosh-cli-* /usr/local/bin/bosh
```
check
```
$ bosh -v
version 5.4.0-891ff634-2018-11-14T00:22:02Z

Succeeded
```

## Install Director VM
```
$ git clone https://github.com/cloudfoundry/bosh-deployment ~/workspace/bosh-deployment
$ vi ~/workspace/bosh-deployment/virtualbox/cpi.yml
    memory: 8196

$ mkdir -p ~/deployments/vbox
$ cd ~/deployments/vbox
$ bosh create-env ~/workspace/bosh-deployment/bosh.yml \
  --state ./state.json \
  -o ~/workspace/bosh-deployment/virtualbox/cpi.yml \
  -o ~/workspace/bosh-deployment/virtualbox/outbound-network.yml \
  -o ~/workspace/bosh-deployment/bosh-lite.yml \
  -o ~/workspace/bosh-deployment/bosh-lite-runc.yml \
  -o ~/workspace/bosh-deployment/uaa.yml \
  -o ~/workspace/bosh-deployment/credhub.yml \
  -o ~/workspace/bosh-deployment/jumpbox-user.yml \
  --vars-store ./creds.yml \
  -v director_name=bosh-lite \
  -v internal_ip=192.168.50.6 \
  -v internal_gw=192.168.50.1 \
  -v internal_cidr=192.168.50.0/24 \
  -v outbound_network_name=NatNetwork
```

## Alias and log into the Director
```
$ export BOSH_CLIENT=admin
$ export BOSH_CLIENT_SECRET=`bosh int ./creds.yml --path /admin_password`
$ bosh alias-env vbox -e 192.168.50.6 --ca-cert <(bosh int ./creds.yml --path /director_ssl/ca)
$ bosh -e vbox env
Using environment '192.168.50.6' as '?'

Name: ...
User: admin

Succeeded
```

## Local Route
```
$ sudo route add -net 10.244.0.0/16     192.168.50.6 # Mac OS X
$ sudo ip route add   10.244.0.0/16 via 192.168.50.6 # Linux (using iproute2 suite)
$ route add           10.244.0.0/16     192.168.50.6 # Windows
```

## Update cloud config
```
$ git clone https://github.com/cloudfoundry/cf-deployment ~/workspace/cf-deployment
$ cd ~/workspace/cf-deployment
$ bosh -e vbox update-cloud-config iaas-support/bosh-lite/cloud-config.yml
```
### Upload a runtime-config
```
$ bosh -e vbox update-runtime-config ~/workspace/bosh-deployment/runtime-configs/dns.yml --name dns
```
>https://github.com/cloudfoundry/cf-deployment/issues/648
>https://github.com/cloudfoundry/cf-deployment/blob/master/deployment-guide.md#upload-a-runtime-config

## Upload stemcell
stemcell 버전은 cf-deployment.yml 파일의 "stemcells" 버전을 확인하여 입력한다.
```
$ bosh -e vbox upload-stemcell https://bosh.io/d/stemcells/bosh-warden-boshlite-ubuntu-xenial-go_agent#v170.9
```

## Deploy CF
```
$ bosh -e vbox -d cf deploy cf-deployment.yml \
   -o operations/bosh-lite.yml \
   --vars-store deployment-vars.yml \
   -v system_domain=bosh-lite.com
```
https://github.com/cloudfoundry/cf-deployment/blob/master/deployment-guide.md
```
$ cf api https://api.bosh-lite.com --skip-ssl-validation
$ export CF_ADMIN_PASSWORD=$(bosh int ./deployment-vars.yml --path /cf_admin_password)
$ cf auth admin $CF_ADMIN_PASSWORD
```

## Release Modify
```
$ git clone https://github.com/cloudfoundry/diego-release ~/workspace/diego-release
$ cf ~/workspace/diego-release
$ git checkout tags/v2.22.0
$ bosh -e vbox releases
$ bosh -e vbox create-release --force
$ bosh -e vbox upload-release
```
modify cf-deployment.yml
```
releases:
- name: diego
#  url: https://bosh.io/d/github.com/cloudfoundry/diego-release?v=2.22.0
  version: 2.22.0+dev.3
  sha1: 0cd26089729d4e526dd27c47191cc3b9cb3189c6
```
deploy
```
$ cd ~/workspace/cf-deployment
$ bosh -e vbox -d cf deploy cf-deployment.yml \
   -o operations/bosh-lite.yml \
   --vars-store deployment-vars.yml \
   -v system_domain=bosh-lite.com
```

## Commands
- 자동복구모드 off
```
$ bosh -e vbox -d cf update-resurrection off
```

- releases
```
$ bosh -e vbox releases
$ bosh -e vbox create-release --force --tarball=diego-2.22.0+dev.4.tgz
$ bosh -e vbox upload-release ./diego-2.22.0+dev.4.tgz
$ bosh -e vbox delete-release diego/2.22.0+dev.4
```
>https://bosh.io/docs/uploading-releases/

- deployments
```
$ bosh -e vbox deployments

#delete
$ bosh -e vbox -d zookeeper delete-deployment --force
```
- vms & instances
```
$ bosh -e vbox vms
$ bosh -e vbox instancesreleases:
```
- ssh
```
$ bosh –e vbox ssh –d {deployment-name} {job-name}
$ bosh –e vbox ssh –d cf nats
```
- tasks
```
$ bosh –e vbox tasks
```
- locks
```
$ bosh -e vbox locks
```


### 참고 BBS 테스트코드 생성
1. Install Protobuf
```
curl -OL https://github.com/protocolbuffers/protobuf/releases/download/v3.6.1/protoc-3.6.1-linux-x86_64.zip
# unzip protoc-3.6.1-linux-x86_64.zip -d /usr/local/ -x readme.txt include/*
# chmod a+x /usr/local/bin/protoc

unzip protoc-3.6.1-linux-x86_64.zip -d protoc3
sudo mv protoc3/bin/* /usr/local/bin/
sudo mv protoc3/include/* /usr/local/include/

sudo chown [user] /usr/local/bin/protoc
sudo chown -R [user] /usr/local/include/google

chmod a+x /usr/local/bin/protoc
```
2. Run Protoc
```
cd ~/workspace/diego-release
source .envrc
src/code.cloudfoundry.org/bbs/scripts/generate_protos.sh
cd src/code.cloudfoundry.org/bbs
```
