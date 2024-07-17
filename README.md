# Update-Storage

Update Storage to 0.3.4
### Luu ý Trước khi cài nên lưu file config.toml lại vì khi cài nó sẽ bị xóa

### Stop Node first
```bash
sudo systemctl stop zgs
```

```bash
rm -rf /root/0g-storage-node
git clone -b v0.3.4 https://github.com/0glabs/0g-storage-node.git
cd $HOME/0g-storage-node
git submodule update --init
cargo build --release
```


### Check version

```bash
$HOME/0g-storage-node/target/release/zgs_node --version
```


### Nếu là  0.3.4 thì chúc mừng ... chúng ta qua bước tiếp theo 

### Chỉnh sửa lại config.toml vì mọi dữ liệu đã mất trong file config.toml 
aaý file đã lưu dán vào  (không chỉnh sửa thêm gì nữa. Nó giống hệt file trước đó).

```bash
nano $HOME/0g-storage-node/run/config.toml
```


### Restart

```bash
sudo systemctl restart zgs
```

### Check logs

```bash
tail -f ~/0g-storage-node/run/log/zgs.log.$(TZ=UTC date +%Y-%m-%d)
```


### Check peer

```bash
curl -X POST http://localhost:5678 -H "Content-Type: application/json" -d '{"jsonrpc":"2.0","method":"zgs_getStatus","params":[],"id":1}'  | jq
```
 
