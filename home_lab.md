# Home Lab Log — SSH, Users, Services & Monitoring

---

## Phase 2: User & Permission Management

Create new user:

```bash
sudo adduser testuser
```

Create new group:

```bash
sudo groupadd testgroup
```

Add user to group:

```bash
sudo usermod -aG testgroup testuser
```

Test file ownership:

```bash
mkdir ~/permtest
cd ~/permtest
echo "hello world" > file1.txt
ls -l
```

Change owner and group:

```bash
sudo chown testuser file1.txt
sudo chgrp testgroup file1.txt
```

Change permissions:

```bash
chmod o-r file1.txt
chmod g+x file1.txt
chmod 755 file1.txt
```

Switch user and try access:

```bash
su - testuser
cd /home/<mainuser>/permtest
cat file1.txt
```

Got **Permission denied**. (Expected since home dirs are private.)

/tmp test:

```bash
mkdir /tmp/permtest
cd /tmp/permtest
echo "hello world" > file.txt
sudo chown testuser:testgroup file.txt

su - testuser
cat /tmp/permtest/file.txt
```

Output: `hello world`

---

## Phase 3: Networking & Services

Install nginx:

```bash
sudo apt install nginx -y
```

Default nginx page available at server IP.

UFW setup:

```bash
sudo ufw allow OpenSSH
sudo ufw allow 'Nginx HTTP'
sudo ufw enable
sudo ufw status
```

Did port forwarding on secondary router again, this time for port 80 → server IP.

Now from main PC I can go to:

```
http://<secondary-router-ip>
```

and see nginx page.

Edit hosts file on PC:

```
192.168.31.50   lab.local
```

Now can use [http://lab.local](http://lab.local) instead of IP.

Check logs:

```bash
sudo tail -f /var/log/nginx/access.log
```

Requests from main PC show up in log.

---

## Phase 4: Monitoring & Logs

System logs:

```bash
journalctl -xe
journalctl -f
```

Syslog:

```bash
sudo tail -f /var/log/syslog
```

htop:

```bash
sudo apt install htop -y
htop
```

iftop:

```bash
sudo apt install iftop -y
sudo iftop
```

Saw nginx traffic when loading the page from main PC.

Nginx logs:

```bash
sudo tail -f /var/log/nginx/access.log
sudo tail -f /var/log/nginx/error.log
```

---
