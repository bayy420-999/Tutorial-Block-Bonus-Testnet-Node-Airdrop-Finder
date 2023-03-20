# Tutorial Block Bonus Testnet Node Airdrop Finder

<p style="font-size:14px" align="right">
<a href="https://t.me/airdropfind" target="_blank">Join Telegram Airdrop Finder<img src="https://user-images.githubusercontent.com/50621007/183283867-56b4d69f-bc6e-4939-b00a-72aa019d1aea.png" width="30"/></a>
</p>

<p align="center">
  <img height="auto" width="auto" src="https://raw.githubusercontent.com/bayy420-999/airdropfind/main/NavIcon.png">
</p>

## Referensi

[Dokumen resmi](https://bblocklabs.github.io/BonusBlock-docs/docs/running-a-node/prerequisites)

[Telegram Bonus Block](https://t.me/bonusblock)

[Faucet](https://faucet.bonusblock.io/)

## Persyaratan hardware & software

### Persyaratan hardware

| Komponen | Spesifikasi minimal |
|----------|---------------------|
|CPU|4 Cores|
|RAM|16 GB DDR4 RAM|
|Penyimpanan|500 GB HDD|
|Koneksi|10Mbit/s port|

| Komponen | Spesifikasi rekomendasi |
|----------|---------------------|
|CPU|32 Cores|
|RAM|32 GB DDR4 RAM|
|Penyimpanan|2 x 1 TB NVMe SSD|
|Koneksi|1 Gbit/s port|

### Persyaratan software/OS

| Komponen | Spesifikasi minimal |
|----------|---------------------|
|Sistem Operasi|Ubuntu 16.04|

| Komponen | Spesifikasi rekomendasi |
|----------|---------------------|
|Sistem Operasi|Ubuntu 22.04|

## Dependensi yang dibutuhkan
   * Go versi 1.19.1 keatas
   * wget
   * curl
   * git

* Install wget, curl, git, make
  ```console
  sudo apt-get update && sudo apt-get upgrade
  apt-get install wget curl git make
  ```

* Install Go
  1. Download Go
     ```console
     wget https://go.dev/dl/go1.19.1.linux-amd64.tar.gz
     ```
  2. Install Go
     ```console
     rm -rf /usr/local/go && tar -C /usr/local -xzf go1.19.1.linux-amd64.tar.gz
     ```
  3. Load environment variables
     ```console
     export PATH=$PATH:/usr/local/go/bin
     ```
  4. Cek apakah Go sudah terinstall
     ```console
     go version
     ```


## Setup Node

### Download dan install Node

1. Download Node
   ```console
   git clone https://github.com/BBlockLabs/BonusBlock-chain
   ```

2. Pindah ke direktori Node
   ```console
   cd BonusBlock-chain
   ```

3. Build Node
   ```console
   make build
   ```

4. Cek apakah Node sudah terinstal
   ```console
   bonus-blockd
   ```

### Menjalankan Node

1. Inisialisasi konfigurasi Bode
   ```console
   bonus-blockd init <nama_validator> --chain-id=blocktopia-01
   ```
   Ganti nama validator

2. Update genesis.json
   ```console
   rm ~/.bonusblock/config/genesis.json
   curl https://bonusblock-testnet.alter.network/genesis? | jq '.result.genesis' > ~/.bonusblock/config/genesis.json
   ```

3. Buat daemon
   ```console
   sudo tee /etc/systemd/system/bonus-blockd.service > /dev/null <<EOF
   [Unit]
   Description=bonus-blockd
   After=network-online.target
   [Service]
   User=$USER
   ExecStart=$(which bonus-blockd) start
   Restart=always
   RestartSec=3
   LimitNOFILE=4096
   [Install]
   WantedBy=multi-user.target
   EOF
   ```

4. Aktifkan daemon
   ```console
   sudo systemctl enable bonus-blockd
   ```

5. Start Node
   ```console
   bonus-blockd start --p2p.seeds e5e04918240cfe63e20059a8abcbe62f7eb05036@bonusblock-testnet-p2p.alter.network:26656
   ```

### Daftar validator

1. Buat wallet
   ```console
   bonus-blockd keys add wallet
   ```
   Simpan mnemonic

2. Klaim faucet
   Klaim faucet di https://faucet.bonusblock.io/
3. Cek status Node
   ```console
   bonus-blockd status | jq
   ```
   Cek apakah `"catching_up": false`, kalo udah lanjut langkah berikutnya

4. Daftarkan validator
   ```console
   bonus-blockd tx staking create-validator \
     --from=wallet \
     --amount=1000000000ubonus \
     --fees=0ubonus --pubkey $(bonus-blockd tendermint show-validator) \
     --chain-id=blocktopia-01 \
     --commission-rate="0.10" \
     --commission-max-rate="0.20" \
     --commission-max-change-rate="0.01" \
     --min-self-delegation="1" \
     --gas="auto" \
     --gas-adjustment=1.15 \
   ```

## Perintah berguna

### Menghentikan Node
```console
systemctl stop bonus-blockd
```

### Restart Node
```console
systemctl restart bonus-blockd
```

### Hapus Node
```console
systemctl stop bonus-blockd && systemctl disable bonus-blockd
rm -rf $HOME/.bonus-block $HOME./BonusBlock-chain $HOME/go/bin/bonus-blockd
```

### Cek log
```console
journalctl -fu bonus-blockd -o cat
```

### Cek status Node
```console
bonus-blockd status | jq
```

### Cek valoper address
```console
bonus-blockd keys show wallet --bech val -a
```

### Unjail validator
```console
bonus-blockd tx slashing unjail $(bonus-blockd keys show wallet --bech val -a) \
  --chain-id=blocktopia-01 \
  --from=wallet
```

## Troubleshoot
Reserved
