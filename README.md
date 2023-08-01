<img alt='ChihuahuaChain logo' src="https://github.com/ChihuahuaChain/resources/blob/main/logo/logo_transparent_notext.png?raw=true" width="150"/>

# ChihuahuaChain
##### _The Cosmos MEME Coin_
Stay up to date with the latest news on our Socials
 - Join our [Telegram Community](https://t.me/chihuahua_cosmos)
 - Join our [Discord](https://discord.gg/chihuahua)
 - Follow us on [Twitter](https://twitter.com/ChihuahuaChain)
 - Check out our [Medium](https://medium.com/@chihuahuachain)

# Join the Testnet!

# Node Installation

- #### Install Prerequisites

```bash
# update the local package list and install any available upgrades
sudo apt-get update && sudo apt upgrade -y

# install toolchain and ensure accurate time synchronization
sudo apt-get install make build-essential gcc git jq chrony -y
```

- #### Install Go

```bash
# download the latest version
wget https://go.dev/dl/go1.20.6.linux-amd64.tar.gz

# remove old version (if any)
sudo rm -rf /usr/local/go

# install the new version
sudo tar -C /usr/local -xzf go1.20.6.linux-amd64.tar.gz
```

- #### Configure Environmental Variables
```bash
# run these commands
cat <<EOF >> ~/.profile
export GOROOT=/usr/local/go
export GOPATH=$HOME/go
export GO111MODULE=on
export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin
EOF

source ~/.profile

go version
```
The output should be `go version go1.20.6 linux/amd64`

- #### Install Chihuahua from sources

```bash
# run these commands
git clone https://github.com/ChihuahuaChain/chihuahua.git
cd chihuahua
git fetch --tags
git checkout v4.2.3
make install
```

To verify the installation you can run `chihuahuad version` and it should return `v4.2.3`

- #### Initialize the Chain
Replace `$MONIKERNAME` with your choosen node name

`chihuahuad init $MONIKER_NAME --chain-id woofnet-1`

- #### Download the Genesis

```bash
wget -O ~/.chihuahuad/config/genesis.json https://raw.githubusercontent.com/ChihuahuaChain/testnet/main/genesis.json

```

- #### Add Persistent Peers

```bash
peers="dd8b324412ebc6e3870432d20a272f684df74967@testnet.chihuahua.wtf:36656"
sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" ~/.chihuahuad/config/config.toml
```

- #### Update minimum-gas-price in app.toml

```bash
sed -i.bak 's/minimum-gas-prices =.*/minimum-gas-prices = "1uhuahua"/' $HOME/.chihuahuad/config/app.toml
```

- #### Setting up Cosmovisor

Install cosmovisor
```bash
go install github.com/cosmos/cosmos-sdk/cosmovisor/cmd/cosmovisor@latest

which cosmovisor

# should return
'/home/<your-user>/go/bin/cosmovisor'

# run these commands
cat <<EOF >> ~/.profile
export DAEMON_NAME=chihuahuad
export DAEMON_HOME=$HOME/.chihuahuad
EOF

source ~/.profile

echo $DAEMON_NAME

# should return
'chihuahuad'

# create the directories
mkdir -p $DAEMON_HOME/cosmovisor/genesis/bin
mkdir -p $DAEMON_HOME/cosmovisor/upgrades

# check the binary path with
which chihuahuad

# this should return
'/home/your-user/go/bin/chihuahuad'

# copy the binary into
cp $(which chihuahuad) $DAEMON_HOME/cosmovisor/genesis/bin
```
Set up the service file

```bash
sudo nano /etc/systemd/system/woofnet.service

# paste and edit <your-user> with your username
[Unit]
Description=Chihuahua Testnet Daemon (cosmovisor)
After=network-online.target

[Service]
User=<your-user>
ExecStart=/home/<your-user>/go/bin/cosmovisor run start
Restart=always
RestartSec=3
LimitNOFILE=4096
Environment="DAEMON_NAME=chihuahuad"
Environment="DAEMON_HOME=/home/<your-user>/.chihuahuad"
Environment="DAEMON_ALLOW_DOWNLOAD_BINARIES=false"
Environment="DAEMON_RESTART_AFTER_UPGRADE=true"
Environment="DAEMON_LOG_BUFFER_SIZE=512"
Environment="UNSAFE_SKIP_BACKUP=True"

[Install]
WantedBy=multi-user.target
```

Enable the service

```bash
sudo -S systemctl daemon-reload
sudo -S systemctl enable woofnet
```

- #### Start the node

You can start the node by running
```bash
sudo systemctl start woofnet

# check the logs by running
journalctl -u woofnet -f
```
The node will take some time to catch-up with the blockchain.
You can follow the blocks being indexed by rnning

```bash
journalctl -u woofnet -f | grep indexed
```

# Join the Validators _(testnet)_

ChihuahuaChain Governance [voted a proposal](https://www.mintscan.io/chihuahua/proposals/3) enabling the minimum 5% Commission enforced by the blockchain, this enforcement works in testnet too!

```bash
# create a new wallet for the validator

chihuahuad keys add <key-name>

# save the seed phrase (mnemonic) in a safe place
# copy the 'chihuahua...' address and send some HUAHUA
# in order to pay for the validator creation's transaction

# Make sure the Validator has fully synced before running
chihuahuad tx staking create-validator \
  --from "<key-name>" \
  --amount "1000000uhuahua" \
  --pubkey "$(chihuahuad tendermint show-validator)" \
  --chain-id "woofnet-1" \
  --moniker "<moniker>" \
  --commission-max-change-rate 0.01 \
  --commission-max-rate 0.20 \
  --commission-rate 0.10 \
  --min-self-delegation 1 \
  --details "<details>" \
  --security-contact "<contact>" \
  --website "<website>" \
  --gas-prices "1uhuahua"

# Make sure to backup the priv_validator_key.json file in your
# /home/<your-user>/.chihuahuad/config directory
# and store it in a safe place
```

**Congratulation!** Your Testnet node should be up and running

_Make sure to join our [Discord](https://discord.gg/chihuahua) and join our Testnet channel so we can send you some testnet HUAHUA for you to play with! Woof!

---
