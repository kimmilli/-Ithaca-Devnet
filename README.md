# 🚀 Ithaca Devnet Node Setup Guide  

## 🔹 Server Requirements  
The following specs should be enough to run the node:  
- **CPU:** 2 CPUs  
- **RAM:** 4 GB  
- **Disk:** 40 GB SSD  

⚠️ **Note:** This is a local network for development purposes. It requires a beacon chain to connect to.  

---

## 🔹 Server Preparation  

### 1️⃣ Update Packages  
```bash
sudo apt-get update && sudo apt-get upgrade -y
```  

### 2️⃣ Install Required Utilities  
```bash
sudo apt install git -y
```  

### 3️⃣ Install Rust  
```bash
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs/ | sh
```  
If you already have Rust installed, you can update it:  
```bash
rustup update
```  
When you see the prompt **"Proceed with standard installation"**, simply press **Enter**.  

### 4️⃣ Configure Rust Path  
```bash
. "$HOME/.cargo/env"
```  

### 5️⃣ Check Rust Version  
```bash
rustup --version
```  

---

## 🔹 Node Installation  

### 1️⃣ Clone the Ithaca Repository  
```bash
git clone https://github.com/ithacaxyz/odyssey.git
```  

### 2️⃣ Go to the project folder and install the node  
```bash
cd odyssey && \
cargo install --path bin/odyssey
```  

### 3️⃣ Create the keys folder  
```bash
mkdir keys
```  

### 4️⃣ Generate the `jwt.hex` file  
```bash
openssl rand -hex 32 > /root/odyssey/keys/jwt.hex
```  
⚠️ **Make sure to save this file!** It contains a secret key for authentication.  

---

## 🔹 Creating the Service File  

Create the **ithacad.service** file and add the following configuration:  
```bash
tee /etc/systemd/system/ithacad.service > /dev/null <<EOF
[Unit]
Description=Ithaca Devnet Node
After=network.target

[Service]
User=$USER
WorkingDirectory=$HOME/odyssey
ExecStart=$(which odyssey) node --chain etc/odyssey-genesis.json --rollup.sequencer-http https://odyssey.ithaca.xyz --http --http.port 8548 --ws --ws.port 8547 --authrpc.port 9551 --port 30304 --authrpc.jwtsecret /root/odyssey/keys/jwt.hex
Restart=on-failure
RestartSec=3
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF
```  

---

## 🔹 Start and Check the Node  

### 1️⃣ Reload systemd and start the node  
```bash
systemctl daemon-reload && \
systemctl enable ithacad && \
systemctl start ithacad
```  

### 2️⃣ Check the node’s status  
```bash
systemctl status ithacad
```  

If everything works fine, the status should show **"active (running)"**.  

### 3️⃣ Check the logs  
```bash
journalctl -u ithacad -f -o cat
```  

---

🔥 **Done! The Ithaca Devnet Node is now successfully installed and running.**  


