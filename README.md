# cloudfoundry-deployment

## bosh-lite

### Pre-Reqs
[go](go)  
[godep](godep)

#### go
```
wget https://storage.googleapis.com/golang/go1.8.3.linux-amd64.tar.gz
sudo tar -C /usr/local -xzf go1.8.3.linux-amd64.tar.gz
echo 'export GOROOT=/usr/local/go' >> ~/.bash_profile
echo 'export PATH=$PATH:$GOROOT/bin' >> ~/.bash_profile

. ~/.bash_profile
```

#### godep
```
go get github.com/tools/godep
```
