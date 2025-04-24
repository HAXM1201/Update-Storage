# Storage V3 Galileo


### 1. Install dependencies for building from source
```bash
sudo apt-get update
sudo apt-get install clang cmake build-essential openssl pkg-config libssl-dev
```

### 2. install go

```bash
cd $HOME && \
ver="1.22.0" && \
wget "https://golang.org/dl/go$ver.linux-amd64.tar.gz" && \
sudo rm -rf /usr/local/go && \
sudo tar -C /usr/local -xzf "go$ver.linux-amd64.tar.gz" && \
rm "go$ver.linux-amd64.tar.gz" && \
echo "export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin" >> ~/.bash_profile && \
source ~/.bash_profile && \
go version
```


### 3. install rustup

```bash
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
```

```bash
. "$HOME/.cargo/env"
```

### 4. Download and install binary

```bash
git clone -b v1.0.0 https://github.com/0glabs/0g-storage-node.git
```


```bash
cd 0g-storage-node

# Build in release mode
cargo build --release
```


### 5. Config:

```bash
curl -o $HOME/0g-storage-node/run/config-testnet-turbo.toml https://cdn.bangcode.id/0g/v3_config.toml
```
### edit tesnet tubo config file (remove # in the following entries)

```
# Network Config Options
network_dir = "network"
network_listen_address = "0.0.0.0"

# DB Config Options
db_dir = "db"

# Log configuration file.
log_config_file = "log_config"

#Log directory.
log_directory = "log"

# transaction gas fee.
miner_key = ""

# RPC Config Options
[rpc]

# Whether to provide RPC service.
 enabled = true

# HTTP server address to bind for public RPC.
 listen_address = "0.0.0.0:5678"
```

### 6. Set your miner key:

```bash
printf '\033[34mEnter your private key: \033[0m' && read -s PRIVATE_KEY
```
```bash
sed -i 's|^\s*#\?\s*miner_key\s*=.*|miner_key = "'"$PRIVATE_KEY"'"|' $HOME/0g-storage-node/run/config-testnet-turbo.toml && echo -e "\033[32mPrivate key has been successfully added to the config file.\033[0m"
```

### 7. Verifying Configuration Changes

```bash
grep -E "^(network_dir|network_enr_address|network_enr_tcp_port|network_enr_udp_port|network_libp2p_port|network_discovery_port|rpc_listen_address|rpc_enabled|db_dir|log_config_file|log_contract_address|mine_contract_address|reward_contract_address|log_sync_start_block_number|blockchain_rpc_endpoint|auto_sync_enabled|find_peer_timeout)" $HOME/0g-storage-node/run/config-testnet-turbo.toml
```

### 8. create service
```bash
sudo tee /etc/systemd/system/zgs.service > /dev/null <<EOF
[Unit]
Description=ZGS Node
After=network.target

[Service]
User=$USER
WorkingDirectory=$HOME/0g-storage-node/run
ExecStart=$HOME/0g-storage-node/target/release/zgs_node --config $HOME/0g-storage-node/run/config-testnet-turbo.toml
Restart=on-failure
RestartSec=10
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF
```

### 9. Start node
```bash
sudo systemctl daemon-reload && \
sudo systemctl enable zgs && \
sudo systemctl restart zgs && \
sudo systemctl status zgs
```


### Check logs

```bash
tail -f ~/0g-storage-node/run/log/zgs.log.$(TZ=UTC date +%Y-%m-%d)
```


### Check peer

```bash
while true; do
    response=$(curl -s -X POST http://localhost:5678/ -H "Content-Type: application/json" -d '{"jsonrpc":"2.0","method":"zgs_getStatus","params":[],"id":1}')
    logSyncHeight=$(echo $response | jq '.result.logSyncHeight')
    connectedPeers=$(echo $response | jq '.result.connectedPeers')
    echo -e "logSyncHeight: \033[32m$logSyncHeight\033[0m, connectedPeers: \033[34m$connectedPeers\033[0m"
    sleep 5;
done
```
 
### This guide contains data processed by josephtran
