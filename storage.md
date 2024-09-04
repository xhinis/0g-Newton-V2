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


---

### ☀️ Required Installations
```bash
sudo apt update && sudo apt upgrade -y
sudo apt install curl git wget htop tmux build-essential jq make pkg-config libssl-dev lz4 gcc unzip -y
```

### ☀️ Go Installation
```bash
cd $HOME
VER="1.21.3"
wget "https://golang.org/dl/go$VER.linux-amd64.tar.gz"
sudo rm -rf /usr/local/go
sudo tar -C /usr/local -xzf "go$VER.linux-amd64.tar.gz"
rm "go$VER.linux-amd64.tar.gz"
[ ! -f ~/.bash_profile ] && touch ~/.bash_profile
echo "export PATH=$PATH:/usr/local/go/bin:~/go/bin" >> ~/.bash_profile
source $HOME/.bash_profile
[ ! -d ~/go/bin ] && mkdir -p ~/go/bin
```

### ☀️ Install Rust
**Note:** Choose option 1
```bash
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
```
```bash
source ~/.profile
```
```bash
source ~/.cargo/env
```

### ☀️ Clone the Repository
```bash
cd
systemctl stop zgsd
mv 0g-storage-node 0g-storage-nodeydk4
```
```bash
git clone https://github.com/0glabs/0g-storage-node.git
cd $HOME/0g-storage-node
git checkout tags/v0.4.6
```

### ☀️ Build the Project
**Note:** This might take a while.
```bash
git submodule update --init
cargo build --release
```
```bash
$HOME/0g-storage-node/target/release/zgs_node --version
```

**Note:** Enter your private key in the service file. If you use your own RPC, make sure your validator snapshot start from height 0

**Note:**  If you are running storage validator on same server, you can get your rpc by using that command.

```bash
sed -n '/\[json-rpc\]/,/\[.*\]/ { /^\s*address\s*=\s*".*"/p }' $HOME/.0gchain/config/app.toml
```
EXAMPLE Output : address = "0.0.0.0:26145" ( storage on same server )
If your storage on different server YOURIP:YOURPORT from the command above.

### ☀️ Setup the Service
```bash
sudo tee /etc/systemd/system/zgsd.service > /dev/null <<EOF
[Unit]
Description=ZGS Node
After=network.target

[Service]
User=root
WorkingDirectory=$HOME/0g-storage-node/run
ExecStart=$HOME/0g-storage-node/target/release/zgs_node --config config-testnet-standard.toml --miner-key YOUR-PRIVATEKEY --blockchain-rpc-endpoint https://YOURRPC/
Restart=on-failure
RestartSec=10
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF
```

### ☀️ Start the Service
```bash
sudo systemctl daemon-reload
sudo systemctl enable zgsd
sudo systemctl restart zgsd
```

### ☀️ Check Your Log List
```bash
ls ~/0g-storage-node/run/log/
```

### ☀️ Check the Latest Log
```bash
tail -f -n 20 "$ZGS_LOG_DIR/$(ls -Art $ZGS_LOG_DIR | tail -n 1)"
```
OR
```bash
tail -f ~/0g-storage-node/run/log/zgs.log.$(TZ=UTC date +%Y-%m-%d)
```

To track the exact match through tx:
```bash
tail -f ~/0g-storage-node/run/log/zgs.log.$(TZ=UTC date +%Y-%m-%d) | grep tx_seq
```

### ☀️ Check Node Status
```bash
curl -X POST http://localhost:5678 -H "Content-Type: application/json" -d '{"jsonrpc":"2.0","method":"zgs_getStatus","params":[],"id":1}' | jq
```

---

Feel free to ask if you need any further modifications!
