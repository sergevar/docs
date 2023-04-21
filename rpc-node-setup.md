# cvn-rpc
---

## Install Dependencies

### Update and Upgrade System

```bash
sudo apt update
sudo apt upgrade
```

### Install golang

```bash
sudo snap install go --classic
# Test Installation
go version
```

### Install apt packages

```bash
sudo apt-get install git
sudo apt-get install gcc
sudo apt-get install make
```

## Setup Sync until First Chain Upgrade

### Install cvnd

```bash
git clone https://github.com/cvn-network/cvn.git
cd cvn
git checkout genesis
make install
sudo mv $HOME/go/bin/cvnd /usr/bin/
# Test Installation
cvnd version --log_level error --long | head -n6
```

### Initialize cvnd

```bash
cvnd init <MONIKER> --chain-id <CHAIN_ID>
cd ~/.cvnd/config
rm genesis.json
# Download genesis from https://github.com/cvn-network/docs/genesis.json to same location
```

### Set Config

```bash
# Set config.toml
vim ~/.cvnd/config/config.toml
# Add peers to persistent_peers list. This is under the P2P section.
persistent_peers = "<node_id>@<ip>:26656"

# IF NEEDED: Can enable prometheus. This is under the Instrumentation section.
prometheus = true
# Metrics exposed on Port: 26660

# Set app.toml
vim ~/.cvnd/config/app.toml
# Set minimum-gas-prices
minimum-gas-prices = "0.0001acvnt"

# IF NEEDED: Can setup unpruned node. This is under the Base Configuration section.
pruning = "nothing"
```

### Setup Systemd

```bash
# Create systemd service
sudo vim /etc/systemd/system/cvnd.service
# Paste below text
[Unit]
Description=CVN RPC Node
After=network.target

[Service]
Type=simple
User=ubuntu
WorkingDirectory=/home/ubuntu/
ExecStart=/usr/bin/cvnd start --trace --log_level info --json-rpc.api eth,txpool,personal,net,debug,web3 --rpc.laddr "tcp://0.0.0.0:26657" --api.enable --json-rpc.enable
Restart=on-failure
StartLimitInterval=0
RestartSec=3
LimitNOFILE=65535
LimitMEMLOCK=209715200

[Install]
WantedBy=multi-user.target
# Close and Save file

# Reload the service files
sudo systemctl daemon-reload
# Create the symlinlk
sudo systemctl enable cvnd.service
```

## Run Node

### Start Node

```bash
sudo systemctl start cvnd
# Watch logs
journalctl -u cvnd -f
```

### Chain Upgrade

```bash
# Stop Node
sudo systemctl stop cvnd

# Inside cvn repository
git checkout <version_tag>
sudo rm /usr/bin/cvnd
make install
sudo mv $HOME/go/bin/cvnd /usr/bin/

# Restart Node
sudo systemctl start cvnd
```
