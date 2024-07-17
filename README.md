# Node-0G-DA
Hướng dẫn cài đặt 0G-DA-Node
yêu cầu hệ thống
Loại	Yêu cầu
CPU	8+ lõi
ĐẬP	16GB trở lên
Kho	Ổ cứng SSD NVME 1TB
Băng thông	100 MBps để Tải xuống / Tải lên
Cập nhật hệ thống
sudo apt update && sudo apt upgrade -y
sudo apt install curl git wget htop tmux build-essential jq make lz4 gcc unzip -y
Tải xuống Git
git clone https://github.com/0glabs/0g-da-node.git
Cài đặt Rust & Kiểm tra Phiên bản
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
source $HOME/.cargo/env
rustc --version
cài đặt lib

```sudo apt-get update
sudo apt install libssl-dev
sudo apt install pkg-config
sudo apt-get install protobuf-compiler
sudo apt-get install clang
sudo apt-get install llvm llvm-dev```
xây dựng
cd && cd 0g-da-node
cargo build --release
amt tải về
cd dev_support
./download_params.sh
sudo cp -R /root/0g-da-node/dev_support/params /root/0g-da-node/target/release
Bls chìa khóa gen
cd
cd 0g-da-node
cargo run --bin key-gen
ĐỪNG QUÊN SAO CHÉP KHÓA BLS CỦA BẠN!!!
config.toml thiết lập
mv /root/0g-da-node/config_example.toml /root/0g-da-node/config.toml
sudo nano /root/0g-da-node/config.toml
grpc_listen_address = "0.0.0.0:34000" eth_rpc_endpoint = "http://Validator rpc ip:8545" socket_address = "nút ip của bạn:34000" da_entrance_address = "0xDFC8B84e3C98e8b550c7FEF00BCB2d8742d80a69" start_block_number = 802 signer_bls_private_key = "Bls key gen paste" signer_eth_private_key = "validator eth private key"
Tạo Systemd
sudo tee /etc/systemd/system/da.service > /dev/null <<EOF
[Unit]
Description=DA Node
After=network.target

[Service]
User=root
WorkingDirectory=$HOME/0g-da-node/target/release
ExecStart=$HOME/0g-da-node/target/release/server --config $HOME/0g-da-node/config.toml
Restart=on-failure
RestartSec=10
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF
Systemd bắt đầu
sudo systemctl daemon-reload && \
sudo systemctl enable da && \
sudo systemctl start da
Nhật ký hệ thống
sudo journalctl -u da -f -o cat
