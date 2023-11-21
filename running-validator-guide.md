running-validator-guide.md
# Running cvnd

At present, the CVNd fully supports installation on linux distributions. For the purpose of this instruction set, we'll be using Ubuntu 20.04.3 LTS. 

## Hardware Requirements

2 core CPU 

8 G RAM 

200G SSD hard driver

## Software Requirements

Please execute the following statement in the bash command line. You need to use an account with root permissions.

```bash
sudo apt-get update
sudo apt-get install docker.io htop screen jq -y
sudo curl -L "https://github.com/docker/compose/releases/download/v2.2.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
sudo usermod -a -G docker $(whoami)
sudo chmod 666 /var/run/docker.sock
```

## Deploy cvn node

### Pull images

```bash
docker pull ghcr.io/cvn-network/cvn-cosmovisor:2.0.0
```

### Downloading genesis.json and config

```bash
mkdir -p ~/.cvn/config/
cd ~/.cvnd/config/
wget -c https://raw.githubusercontent.com/cvn-network/cvn/release/v1.0.x/networks/mainnet/config/genesis.json -O ~/.cvnd/config/genesis.json
wget -c https://raw.githubusercontent.com/cvn-network/cvn/release/v1.0.x/networks/mainnet/config/config.toml -O ~/.cvnd/config/config.toml
```

### Downloding cvn snapshot files.

[backup_20231119](https://cvn-data-snapshot.s3.ap-northeast-1.amazonaws.com/backup_20231119.tar.gz)

Unzip this file to `~/.cvnd/`.

### Running a node

```bash
docker run -it --name cvn \
    -v ~/.cvnd/data:/root/.cvnd/data \
    -v ~/.cvnd/config:/root/.cvnd/config \
    -p 0.0.0.0:26656:26656 -p 127.0.0.1:26657:26657 -p 127.0.0.1:1317:1317 -p 127.0.0.1:8545:8545 \
    ghcr.io/cvn-network/cvn-cosmovisor:2.0.0 \
    cosmovisor run start --minimum-gas-prices=100000000acvnt \
    --rpc.laddr tcp://0.0.0.0:26657 \
    --json-rpc.address 0.0.0.0:8545 
```

# Create validator

## Initialize node

```bash
# You need to change it to your own key name and create key, during which you will enter a password.
cvnd keys add <your-own-key-name>

# The following content will be entered, please keep this information properly.
- address: cvn1vs78p5r3g5xllhmkz46ft8l40m0m7mvzmjcjl8
  name: node4
  pubkey: '{"@type":"/ethermint.crypto.v1.ethsecp256k1.PubKey","key":"AhICaiDdfV8eqzn1cfiez6TeZwGAo5F04j4J8eolLGPf"}'
  type: local
```

## Query the address of the node

```bash
# You need to change it to your own key name and create key, during which you will enter a password.
cvnd debug addr "$(cvnd keys show <your-own-key-name> --address )"

# The following address information will be output
Address (hex): 643C70D071450DFFDF761574959FF57EDFBF6D82
Address (EIP-55): 0x643c70d071450DffDF761574959FF57edfBF6D82
Bech32 Acc: cvn1vs78p5r3g5xllhmkz46ft8l40m0m7mvzmjcjl8
Bech32 Val: cvnvaloper1vs78p5r3g5xllhmkz46ft8l40m0m7mvzmjvpn2
```

## Transfer cvn to this account

There are two ways to transfer cvn:

1. transafer by cvnd command:

```bash
cvnd tx bank send <your-own-key-name> \
<Bech32 Acc> <transfer amount>cvnt --gas=auto --gas-adjustment=1.5 --gas-prices="7acvnt" \
--broadcast-mode block \
--from <your-own-key-name> --yes
```

2. transafer by metamask

The first address is the string output by "Address (hex):" queried in the previous chapter "Query the address of the node". You need to add the prefix "0x".

The following command can query the current account balance:

```bash
cvnd query bank balances <Bech32 Acc> \
--output json | jq
```

## Create validator

### create validator

```bash
# amount=50cvnt Indicates the staking amount initialized by this validator;
# moniker="<show-name> Here is the custom node name, which can be seen in the verification list in the future.
# commission Set validator revenue proportion configuration
# min-self-delegation="50" Minimum agent pledge amount
cvnd tx staking create-validator \
--amount=50cvnt \
--pubkey=$(cvnd tendermint show-validator) \
--moniker="<show-name>" \
--chain-id=cvn_2032-1 \
--commission-rate="0.05" \
--commission-max-rate="0.10" \
--commission-max-change-rate="0.01" \
--min-self-delegation="50" \
--gas="auto" \
--gas-prices="7acvnt"  \
--from=<your-own-key-name>
```

# Maintain validator

## View validator via website

View validator via [website](https://dao.cvn.io/conscious/staking)

## Query account balances by address

```bash
cvnd query bank balances <your-Bech32-Acc> --output json | jq
```

## Query delegator rewards

```bash
cvnd query distribution rewards <your-Bech32-Acc> \
<your-Bech32-Val> --output json | jq
```

## Withdraw all rewards from a given delegation address

```bash
cvnd tx distribution withdraw-rewards <your-Bech32-Val> \
--commission \
--gas=auto --gas-adjustment=1.5 --gas-prices="7acvnt" \
--broadcast-mode block \
--from <your-own-key-name> --yes
```

## Query validator status

```bash
cvnd query staking validator "<your-Bech32-Val>" --output json |jq
```

## unjail validator 

```bash
cvnd tx slashing unjail --from <your-own-key-name> --gas=auto --gas-adjustment=1.5 --gas-prices="7acvnt"
```
