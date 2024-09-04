

# 0G

![0G Image](https://i.imgur.com/5SFSdi8.png)

---

## Community Links

- [Discord](https://discord.com/invite/0glabs)
- [0G Labs Twitter](https://twitter.com/0G_labs)
- [0G Website](https://0g.ai/)
- [0G Blog](https://blog.0g.ai/)
- [0G GitBook/Docs](https://zerogravity.gitbook.io/0g-doc/)
- [0G Telegram](https://t.me/web3_0glabs)
- [Blockchain Explorer](https://explorer.dksnodes.com/0G-Testnet.newton)

---

## 💻 System Requirements

| Components  | Minimum Requirements |
|-------------|----------------------|
| CPU         | 4 Cores               |
| RAM         | 8+ GB                 |
| Storage     | 400 GB SSD            |

### ☀️ Required Installations
```bash
sudo apt update && sudo apt upgrade -y
sudo apt install curl git wget htop tmux build-essential jq make lz4 gcc unzip clang cmake -y
```

### ☀️ Go Installation
```bash
cd $HOME
VER="1.22.3"
wget "https://golang.org/dl/go$VER.linux-amd64.tar.gz"
sudo rm -rf /usr/local/go
sudo tar -C /usr/local -xzf "go$VER.linux-amd64.tar.gz"
rm "go$VER.linux-amd64.tar.gz"
[ ! -f ~/.bash_profile ] && touch ~/.bash_profile
echo "export PATH=$PATH:/usr/local/go/bin:~/go/bin" >> ~/.bash_profile
source $HOME/.bash_profile
[ ! -d ~/go/bin ] && mkdir -p ~/go/bin
```

### ☀️ Download Necessary Files
```bash
wget https://zgchaind-test.s3.ap-east-1.amazonaws.com/0gchaind-linux-v0.3.0
chmod +x ./0gchaind-linux-v0.3.0
mkdir -p /root/.0gchain/cosmovisor/upgrades/v0.3.0/bin
sudo cp -r ./0gchaind-linux-v0.3.0 /root/.0gchain/cosmovisor/upgrades/v0.3.0/bin/0gchaind
```
```bash
mkdir -p $HOME/.0gchain/cosmovisor/genesis/bin
mv /root/0gchaind-linux-v0.3.0 $HOME/.0gchain/cosmovisor/genesis/bin/0gchaind
```
```bash
wget https://github.com/0glabs/0g-chain/releases/download/v0.3.1.alpha.1/0gchaind-linux-v0.3.1.alpha.1
chmod +x ./0gchaind-linux-v0.3.1.alpha.1
mkdir -p /root/.0gchain/cosmovisor/upgrades/v0.3.1/bin
sudo mv ./0gchaind-linux-v0.3.1.alpha.1 /root/.0gchain/cosmovisor/upgrades/v0.3.1/bin/0gchaind
```

### ☀️ System Linking
```bash
sudo ln -s $HOME/.0gchain/cosmovisor/genesis $HOME/.0gchain/cosmovisor/current -f
sudo ln -s $HOME/.0gchain/cosmovisor/current/bin/0gchaind /usr/local/bin/0gchaind -f
```

### ☀️ Install Cosmovisor
```bash
go install cosmossdk.io/tools/cosmovisor/cmd/cosmovisor@v1.5.0
```

### ☀️ Create a Service
```bash
sudo tee /etc/systemd/system/0gchaind.service > /dev/null << EOF
[Unit]
Description=0gchaind Node Service
After=network-online.target

[Service]
User=$USER
ExecStart=$(which cosmovisor) run start --json-rpc.api eth,txpool,personal,net,debug,web3
Restart=on-failure
RestartSec=10
LimitNOFILE=65535
Environment="DAEMON_HOME=$HOME/.0gchain"
Environment="DAEMON_NAME=0gchaind"
Environment="UNSAFE_SKIP_BACKUP=true"
Environment="PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin:$HOME/.0gchain/cosmovisor/current/bin"

[Install]
WantedBy=multi-user.target
EOF
```
```bash
sudo systemctl daemon-reload
sudo systemctl enable 0gchaind.service
```

### ☀️ Node Configuration
```bash
0gchaind config chain-id zgtendermint_16600-2
0gchaind config keyring-backend os
0gchaind config node tcp://localhost:56657
```

### ☀️ Initialize the Node
**NOTE:** Replace `NODE-NAME` with your desired node name.
```bash
0gchaind init NODE-NAME --chain-id zgtendermint_16600-2
```

### ☀️ Update Genesis and Addrbook
```bash
rm ~/.0gchain/config/genesis.json
curl -Ls https://github.com/0glabs/0g-chain/releases/download/v0.2.3/genesis.json > $HOME/.0gchain/config/genesis.json
```

### ☀️ Add Seed Nodes
```bash
PEERS="80fa309afab4a35323018ac70a40a446d3ae9caf@og-testnet-peer.itrocket.net:11656,...(other peers)..."
SEEDS="81987895a11f6689ada254c6b57932ab7ed909b6@54.241.167.190:26656,...(other seeds)..."
sed -i -e "s/^seeds *=.*/seeds = \"$SEEDS\"/; s/^persistent_peers *=.*/persistent_peers = \"$PEERS\"/" $HOME/.0gchain/config/config.toml
```

### ☀️ Set Gas and Pruning
```bash
sed -i -e "s/^pruning *=.*/pruning = \"custom\"/" $HOME/.0gchain/config/app.toml
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"100\"/" $HOME/.0gchain/config/app.toml
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"50\"/" $HOME/.0gchain/config/app.toml
sed -i 's|minimum-gas-prices =.*|minimum-gas-prices = "0ua0gi"|g' $HOME/.0gchain/config/app.toml
sed -i -e "s/prometheus = false/prometheus = true/" $HOME/.0gchain/config/config.toml
```

### ☀️ Indexer Configuration
**NOTE:** If you're setting up a storage node, you might not want to set this to null.
```bash
sed -i -e "s/^indexer *=.*/indexer = \"null\"/" $HOME/.0gchain/config/config.toml
```

### ☀️ Set Ports
```bash
echo "export G_PORT="56"" >> $HOME/.bash_profile
source $HOME/.bash_profile
```
```bash
sed -i.bak -e "s%:1317%:${G_PORT}317%g; s%:8080%:${G_PORT}080%g; s%:9090%:${G_PORT}090%g; ..." $HOME/.0gchain/config/app.toml
```
```bash
sed -i.bak -e "s%:26658%:${G_PORT}658%g; s%:26657%:${G_PORT}657%g; ..." $HOME/.0gchain/config/config.toml
```

### ☀️ Snap Installation

**NOTE:** If you want to use your own RPC on your storage node you have to use snapshot start from blockheight 0

```bash
screen -S 0g
```
```bash
sudo systemctl stop 0gchaind
```
```bash
0gchaind tendermint unsafe-reset-all --home $HOME/.0gchain --keep-addr-book
```
```bash
cp $HOME/.0gchain/data/priv_validator_state.json $HOME/.0gchain/priv_validator_state.json.backup
```
```bash
curl -L https://vps5.josephtran.xyz/0g/0gchain_snapshot.lz4 | sudo lz4 -dc - | sudo tar -xf - -C $HOME/.0gchain
```
```bash
mv $HOME/.0gchain/priv_validator_state.json.backup $HOME/.0gchain/data/priv_validator_state.json
```

### ☀️ Start the Node
```bash
sudo systemctl daemon-reload
sudo systemctl restart 0gchaind
```

### ☀️ View Logs
```bash
sudo journalctl -u 0gchaind.service -f --no-hostname

 -o cat
```

---

## ☀️ Create a Wallet
```bash
0gchaind keys add wallet
```

### ☀️ Recover Existing Wallet
```bash
0gchaind keys add wallet --recover
```

### ☀️ Save Wallet Info
```bash
0gchaind keys show wallet -a
0gchaind keys show wallet --pubkey
```

### ☀️ Faucet
**NOTE:** Add your wallet to metamask from your seed phrases and use your wallet starting with 0x
```bash
[Faucet](https://faucet.0g.ai/) 
```

---

## ☀️ Validator Creation
**NOTE:** Replace `<WALLET_NAME>` with your wallet name and `<MONIKER>` with your desired moniker.
```bash
0gchaind tx staking create-validator --amount 1000000ua0gi --from <WALLET_NAME> --commission-max-change-rate "0.05" --commission-max-rate "0.10" --commission-rate "0.05" --min-self-delegation "1" --pubkey $(0gchaind tendermint show-validator) --moniker "<MONIKER>" --chain-id zgtendermint_16600-2 --fees 20000ua0gi
```

### ☀️ View Validator Details
```bash
0gchaind q staking validator $(0gchaind keys show wallet --bech val -a)
```

### ☀️ Check Sync Status
```bash
0gchaind status 2>&1 | jq .SyncInfo
```

### ☀️ View Wallet Balances
```bash
0gchaind q bank balances $(0gchaind keys show wallet -a)
```

### ☀️ Withdraw Rewards
```bash
0gchaind tx distribution withdraw-all-rewards --from wallet --chain-id zgtendermint_16600-2 --fees 5000ua0gi
```

### ☀️ Withdraw Rewards and Commission
```bash
0gchaind tx distribution withdraw-rewards $(0gchaind keys show wallet --bech val -a) --commission --from wallet --chain-id zgtendermint_16600-2 --fees 5000ua0gi
```

### ☀️ Delegate Tokens to Self
```bash
0gchaind tx staking delegate $(0gchaind keys show wallet --bech val -a) 1000000ua0gi --from wallet --chain-id zgtendermint_16600-2 --fees 5000ua0gi
```

### ☀️ Editing Validator Details
```bash
0gchaind tx staking edit-validator --moniker "<MONIKER>" --identity "KEYBASE_ID" --website "<YOUR_WEBSITE>" --details "<YOUR_VALIDATOR_DETAILS>" --chain-id zgtendermint_16600-2 --from wallet --fees 5000ua0gi
```

### ☀️ Unjail Validator
```bash
0gchaind tx slashing unjail --from wallet --chain-id zgtendermint_16600-2 --fees 5000ua0gi
```
