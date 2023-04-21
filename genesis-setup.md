# Running Validators from Genesis

Here, we describe how to run two validators. This guide can be easily extended to more validators, by running the same steps that are written for validator2 for all extra validators. For example, if we want to run 5 validators, then the steps for validator1 and validator2 remain the same, and the steps for the 3 other validators are the same as that for validator2, and must be run along with validator2.

First, make sure you build the project and cd into the build file, from where you will be running all these commands. This should be done on all the machines of all the validators.

Then, follow these steps. 

First on validator1’s machine

```bash
CHAINID="cvn_8008-1"
MONIKER="localtestnet"
KEYRINGPASS="key1key1"

./cvnd init $MONIKER --chain-id $CHAINID
./cvnd keys add validator1
./cvnd add-genesis-account <address-of-account> 10000000000000000000000acvnt --chain-id $CHAINID

```

`./cvnd init` command simply initialises the chains directory.

`./cvnd keys add` creates a new key/account for you on your system, which you can use for whatever you want. This account will initially not have any tokens.

`./cvnd add-genesis-account` takes an address and a token amount, and gives that account that much token in the genesis file. So, if you want to give three addresses some token in genesis, you will have to run this command thrice, for all three address to give them that token during genesis.

next, on validator2 run -

```bash
./cvnd keys add validator2
```

If you have more validators, then run the above command for all of them too, replacing `validator2` with `validator{i}` where I tells which validator it is. 

then, on validator1

```bash
./cvnd add-genesis-account <validator2address> 10000000000000000000000acvnt --chain-id $CHAINID
cat ~/.cvnd/config/genesis.json
# copy the contents of this json file
```

If you have more validators, then run the above command for all of them too, replacing validator2’s address with the address of the ith validator. This will give these validator accounts their initial tokens.

Now, you will have to copy `~/.cvnd/config/genesis.json` from the machine on running validator1, to all the other validator machines, in the same location (`~/.cvnd/config/genesis.json`). 

on validator2

```bash
vim ~/.cvnd/config/genesis.json
# paste contents from validator1's genesis in vim
./cvnd gentx validator2 100000000000000000000acvnt --chain-id $CHAINID

```

`./cvnd gentx` creates a validator with the given self delegation. This is the command that initialises your validator. If you are running more than two validators, run the above on all other validators too, replacing `validator2` with `validator{i}` for the ith validator.

Now, you will have a gentx transaction written in every validator machine, that you can find at `~/.cvnd/config/gentx` . This file will be of the form `gentx-<validator-id>.json`. you will have to copy this file and put it in `~/.cvnd/config/gentx` of you validator1 machine.

Then, on validator1 run the following.

```bash
./cvnd gentx validator1 100000000000000000000acvnt --chain-id $CHAINID
./cvnd collect-gentxs --chain-id $CHAINID
```

`./cvnd collect-gentxs` collects the gentx transactions and generates a new genesis file from it.

Now, you will have to copy `~/.cvnd/config/genesis.json` from the machine on running validator1, to all the other validator machines, in the same location (`~/.cvnd/config/genesis.json`).

Next, in `~/.cvnd/config/config.toml`, you have to edit the `persistent_peers` variable. Take all your validator nodes, and for each one create a string of the form:

`<validator-id>@public_ip_address:26656` .This string identifies your validator. make a comma separated list of all of these strings, which will contain all your validators. `<validator-id>` can be found in the name of the file of the gentx transaction that that validator created when you ran `gentx` .

it should look something like this -

```bash
1f444ccaf741fbae62e8d229256355c30bcc3f26@172.31.92.88:26656,91d6c30118775cb0a8f91388d89754dbf1e801dc@54.210.230.221:26656,fd8439e1a174f46275ea5604662e76a544961188@3.83.221.22:26656
```

now, for each validator, in the `~/.cvnd/config/config.toml`, edit `persistent_peers` to be equal to this string, after removing that validators id. for example, if you are setting the variable for validator2, then in the above string, validator2’s id should now be present.

Next, run this command on all validator machines. 

```bash
HOMEDIR=$HOME/.cvnd
CONFIG=$HOMEDIR/config/config.toml
APP_TOML=$HOMEDIR/config/app.toml
GENESIS=$HOMEDIR/config/genesis.json
TMP_GENESIS=$HOMEDIR/config/tmp_genesis.json
jq '.app_state["staking"]["params"]["bond_denom"]="acvnt"' "$GENESIS" >"$TMP_GENESIS" && mv "$TMP_GENESIS" "$GENESIS"
jq '.app_state["crisis"]["constant_fee"]["denom"]="acvnt"' "$GENESIS" >"$TMP_GENESIS" && mv "$TMP_GENESIS" "$GENESIS"
jq '.app_state["gov"]["deposit_params"]["min_deposit"][0]["denom"]="acvnt"' "$GENESIS" >"$TMP_GENESIS" && mv "$TMP_GENESIS" "$GENESIS"
jq '.app_state["evm"]["params"]["evm_denom"]="acvnt"' "$GENESIS" >"$TMP_GENESIS" && mv "$TMP_GENESIS" "$GENESIS"
jq '.app_state["inflation"]["params"]["mint_denom"]="acvnt"' "$GENESIS" >"$TMP_GENESIS" && mv "$TMP_GENESIS" "$GENESIS"

jq '.consensus_params.block.max_gas = "10000000"' $GENESIS > $TMP_GENESIS && mv $TMP_GENESIS $GENESIS
# you also have to change the occurance of 127.0.0.1:8545 to 0.0.0.0:8545 in $APP_TOML

sed -i '.bak' 's/seeds = ".*"/seeds = ""/g' $CONFIG
```

And then, finally, run the below command on each validator machine to start the node. 

`./cvnd start --trace --log_level debug --json-rpc.api eth,txpool,personal,net,debug,web3 --rpc.laddr "tcp://0.0.0.0:26657" --api.enable --json-rpc.enable`