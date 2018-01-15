# bosh-lite (CLI v2)

- [Pre-Reqs](#pre-reqs)   
- [Install CLI v2](#install-cli-v2)   
- [Install Director VM](#install-director-vm)   
- [Alias and log into the Director](#alias-and-log-into-the-director)   
- [Local Route](#local-route)   
- [Update cloud config](#update-cloud-config)   
- [Upload stemcell](#upload-stemcell)   
- [Deploy CF](#deploy-cf)
- [Commands](#commands)

## Pre-Reqs
```
sudo apt-get update
```
Install [VirtualBox](https://www.virtualbox.org/wiki/Downloads)


## Install CLI v2
```
$ chmod +x ~/Downloads/bosh-cli-*
$ sudo mv ~/Downloads/bosh-cli-* /usr/local/bin/bosh
```
check
```
$ bosh -v
version 2.0.44-21639e8-2017-10-17T18:49:44Z
```

## Install Director VM
```
$ git clone https://github.com/cloudfoundry/bosh-deployment ~/workspace/bosh-deployment
$ mkdir -p ~/deployments/vbox
$ cd ~/deployments/vbox
$ bosh create-env ~/workspace/bosh-deployment/bosh.yml \
  --state ./state.json \
  -o ~/workspace/bosh-deployment/virtualbox/cpi.yml \
  -o ~/workspace/bosh-deployment/virtualbox/outbound-network.yml \
  -o ~/workspace/bosh-deployment/bosh-lite.yml \
  -o ~/workspace/bosh-deployment/bosh-lite-runc.yml \
  -o ~/workspace/bosh-deployment/jumpbox-user.yml \
  --vars-store ./creds.yml \
  -v director_name="Bosh Lite Director" \
  -v internal_ip=192.168.50.6 \
  -v internal_gw=192.168.50.1 \
  -v internal_cidr=192.168.50.0/24 \
  -v outbound_network_name=NatNetwork
```

## Alias and log into the Director
```
$ bosh alias-env vbox -e 192.168.50.6 --ca-cert <(bosh int ./creds.yml --path /director_ssl/ca)
$ export BOSH_CLIENT=admin
$ export BOSH_CLIENT_SECRET=`bosh int ./creds.yml --path /admin_password`
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

## Upload stemcell
```
$ bosh upload-stemcell https://bosh.io/d/stemcells/bosh-warden-boshlite-ubuntu-trusty-go_agent?v=3468.17
```

## Deploy CF
```
bosh -e vbox -d cf deploy cf-deployment.yml \
   -o operations/bosh-lite.yml \
   --vars-store deployment-vars.yml \
   -v system_domain=bosh-lite.com
```

## Commands
- releases
```
$ bosh -e vbox releases 
```
- deployments
```
$ bosh -e vbox deployments

#delete
$ bosh -e vbox -d zookeeper delete-deployment --force
```
- vms & instances
```
$ bosh -e vbox vms
$ bosh -e vbox instances
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
