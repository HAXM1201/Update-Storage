# Update-Storage

Update Storage to 0.3.4
export config.toml before start because if u do this guide. All data is gone.

Stop Node first

```rm -rf /root/0g-storage-node
git clone -b v0.3.4 https://github.com/0glabs/0g-storage-node.git
cd $HOME/0g-storage-node
git submodule update --init
cargo build --release
```


check version
```$HOME/0g-storage-node/target/release/zgs_node --version
```

it should be 0.3.4

reedit the config.toml cuz all data is gone with your saved config.toml (no additional edit. it's the same as the previous one.
```nano $HOME/0g-storage-node/run/config.toml
```


restart
```sudo systemctl restart zgs
```


check logs
```tail -f ~/0g-storage-node/run/log/zgs.log.$(TZ=UTC date +%Y-%m-%d)
```


Check peer
```curl -X POST http://localhost:5678 -H "Content-Type: application/json" -d '{"jsonrpc":"2.0","method":"zgs_getStatus","params":[],"id":1}'  | jq
```
 
