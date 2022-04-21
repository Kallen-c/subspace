#!/bin/bash

while true
do

# Logo

curl -s https://raw.githubusercontent.com/Kallen-c/utils/main/logo.sh | bash

# Menu

PS3='Select an action: '
options=(
"Prepare server"
"Install node and farmer"
"Node log"
"Farmer log"
"Exit")
select opt in "${options[@]}"
do
case $opt in

"Prepare server")
echo "============================================================"
echo "Preparations have begun"
echo "============================================================"
sudo apt-get update && sudo apt-get upgrade -y
sudo apt-get install wget -y
break
;;

"Install node and farmer")
echo "============================================================"
echo "Write the name of your node"
echo "============================================================"
read NODENAME
echo 'export NODENAME='${NODENAME} >> $HOME/.bash_profile
echo "============================================================"
echo "Enter your wallet address"
echo "============================================================"
read WALLETADDRESS
echo 'export WALLETADDRESS='${WALLETADDRESS} >> $HOME/.bash_profile
source ~/.bash_profile

mkdir $HOME/subspace; \
cd $HOME/subspace && \
wget https://github.com/subspace/subspace/releases/download/snapshot-2022-mar-09/subspace-farmer-ubuntu-x86_64-snapshot-2022-mar-09 -O farmer && \
wget https://github.com/subspace/subspace/releases/download/snapshot-2022-mar-09/subspace-node-ubuntu-x86_64-snapshot-2022-mar-09 -O subspace && \
sudo chmod +x * && \
sudo mv * /usr/local/bin/ && \
cd $HOME && \
rm -Rvf $HOME/subspace

sudo tee <<EOF >/dev/null /etc/systemd/system/subspace.service
[Unit]
Description=subspace
After=network.target

[Service]
Type=simple
User=root
WorkingDirectory=/root/
ExecStart=$(which subspace) \
  --chain testnet \
  --wasm-execution compiled \
  --execution wasm \
  --bootnodes "/dns/farm-rpc.subspace.network/tcp/30333/p2p/12D3KooWPjMZuSYj35ehced2MTJFf95upwpHKgKUrFRfHwohzJXr" \
  --rpc-cors all \
  --rpc-methods unsafe \
  --ws-external \
  --validator \
  --telemetry-url "wss://telemetry.polkadot.io/submit/ 1" \
  --telemetry-url "wss://telemetry.subspace.network/submit 1" \
  --name $NODENAME
Restart=on-failure
RestartSec=3
LimitNOFILE=10000

[Install]
WantedBy=multi-user.target
EOF

sudo tee <<EOF >/dev/null /etc/systemd/system/subspacefarm.service
[Unit]
Description=subspacefarm
After=network.target

[Service]
Type=simple
User=root
WorkingDirectory=/root/
ExecStart=$(which farmer) \
farm --reward-address $WALLETADDRESS
Restart=on-failure
RestartSec=3
LimitNOFILE=10000

[Install]
WantedBy=multi-user.target
EOF

sudo systemctl restart systemd-journald
sudo systemctl daemon-reload
sudo systemctl enable subspace
sudo systemctl enable subspacefarm
sudo systemctl restart subspacefarm
sudo systemctl restart subspace

break
;;

"Node log")
sudo journalctl -n 100 -f -u subspace
break
;;

"Farmer log")
sudo journalctl -n 100 -f -u subspacefarm
break
;;


"Exit")
exit
;;
*) echo "invalid option $REPLY";;
esac
done
done
