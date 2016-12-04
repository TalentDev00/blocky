# Blocky
Ethereum based smart contracts for IoT devices

### Install Dependencies

#### Install Go
Download precompiled Go binaries from [official project site](https://golang.org/dl)

For x86_64 Linux
```
curl -O https://storage.googleapis.com/golang/go1.7.4.linux-amd64.tar.gz
```
For Raspberry Pi Linux
```
curl -O https://storage.googleapis.com/golang/go1.7.4.linux-armv6l.tar.gz
```

Install Go binaries
```
sudo tar -C /usr/local -xzf go$VERSION.$OS-$ARCH.tar.gz
```

Add path to /etc/profile (for a system-wide installation) or $HOME/.profile
```
export PATH=$PATH:/usr/local/go/bin
```

#### Install Ethereum Client
```
git clone https://github.com/ethereum/go-ethereum.git
cd go-ethereum
make geth
sudo cp build/bin/geth /usr/local/bin/geth
```

