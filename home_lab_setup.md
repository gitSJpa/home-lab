# Home Lab Log — SSH Setup

This document is a log of the exact steps I took to configure SSH access and harden my Ubuntu server. Each step includes the command(s) I used, why I did it, and the result.

---

## Install and enable SSH
```bash
sudo apt update
sudo apt install openssh-server -y
sudo sysmctl enable ssh
sudo systemctl start ssh
sudo systemctl status ssh
```
➡️ Installed and enabled SSH service successfully.

---

## Network configuration (static IP)
```bash
ip link    # check interface name
ls /etc/netplan/    # find filename
sudo nano /etc/netplan/filename
```
Changes inside file:
```
dhcp4: no
addresses: [192.168.31.50/24]
gateway4: 192.168.31.1
```
```bash
sudo netplan apply
ip a
```
➡️ Server got static IP 192.168.31.50.

---

## Router configuration
Set up port forwarding on secondary network router (where server is).  
➡️ From main PC on primary network, SSH into server worked successfully.

---

## Generate SSH keys (on main PC)
```bash
ssh-keygen -t ed25519 -f $env:USERPROFILE\.ssh\id_ed25519 -C "comment"
```
➡️ Key pair generated.

Copy public key to server:
```bash
Get-Content $env:USERPROFILE\.ssh\id_ed25519.pub | ssh myuser@192.168.1.92 "mkdir -p ~/.ssh; chmod 700 ~/.ssh; cat >> ~/.ssh/authorized_keys; chmod 600 ~/.ssh/authorized_keys"
```
➡️ Public key copied.

---

## Fix permissions on server
```bash
chmod 700 ~/.ssh
chmod 600 ~/.ssh/authorized_keys
chown -R $(whoami):$(whoami) ~/.ssh
```
➡️ Permissions fixed.

---

## Test SSH with key
```bash
ssh -i ~/.ssh/id_ed25519 myuser@192.168.1.92 -v
```
➡️ Login with key successful.

---

## Backup and harden SSH config
```bash
sudo cp /etc/ssh/sshd_config /etc/ssh/sshd_config.bak
sudo nano /etc/ssh/sshd_config
```
Changes applied:
```
PermitRootLogin no
PubkeyAuthentication yes
PasswordAuthentication no
ChallengeResponseAuthentication no
UsePAM yes
```
Restart service:
```bash
sudo systemctl restart ssh
```
➡️ SSH hardened.

---

## Test SSH again
```bash
ssh -i ~/.ssh/id_ed25519 myuser@192.168.1.92
```
➡️ Connection successful with key authentication only.

---

## Create SSH alias (Windows PC)
```powershell
cd C:\Users\<YourUsername>\.ssh\config
mkdir $env:USERPROFILE\.ssh
notepad $env:USERPROFILE\.ssh\config
```
Config file content:
```
Host myserver
    HostName 192.168.1.92
    User myuser
    IdentityFile C:\Users\jotas\.ssh\id_ed25519
    Port 22
```
Remove `.txt` extension:
```powershell
ren $env:USERPROFILE\.ssh\config.txt config
```
Test:
```powershell
ssh myserver
```
➡️ Works successfully with alias.

---

