---
title: "Part 3: Securing and Hardening the Server"
seoTitle: "Securing and Hardening Your Ubuntu Home Lab Server"
seoDescription: "Harden your Ubuntu home server with firewall rules, SSH hardening, Fail2Ban, Auditd, and secure SFTP access. Complete security checklist."
datePublished: Sun Jun 15 2025 14:00:24 GMT+0000 (Coordinated Universal Time)
cuid: cmbxqg6nb000102jjfxndc4gi
slug: part-3-securing-and-hardening-the-server
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1748866975980/90d86071-7372-4dc1-8092-ef437f135685.png
ogImage: https://cdn.hashnode.com/res/hashnode/image/upload/v1748867027469/88cd651e-407c-4735-a764-1a8980ae9ab0.png
tags: security, ssh, firewall, fail2ban, linux-security

---

Welcome to Part 3 of the Ubuntu home lab series. Now that we have a solid base system with essential tools, it's time to focus on security. In this part, we'll:

* Sync time with NTP
    
* Enable automatic security updates
    
* Harden SSH
    
* Configure firewall rules with iptables
    
* Install Auditd, Fail2Ban, and Lynis
    

> 🛡️ Security is a process, not a one-time task. These steps form a solid baseline.

---

## 🕒 Step 1: Setup Time Synchronisation (NTP)

Ubuntu uses `systemd-timesyncd` for NTP.

1. **Check if enabled:**
    

```bash
systemctl status systemd-timesyncd
```

2. **Start and enable it:**
    

```bash
sudo systemctl start systemd-timesyncd
sudo systemctl enable systemd-timesyncd
```

3. **Check sync status:**
    

```bash
timedatectl status
```

4. **Configure NTP servers:**
    
    Having `systemd-timesyncd` will not sync time on the server. We need to configure it to update system time automatically by specifying which NTP server we need to use.
    
    By default, `systemd-timesyncd` uses NTP servers configured in `/etc/systemd/timesyncd.conf`. We can modify or add custom NTP servers.
    

```bash
sudo nano /etc/systemd/timesyncd.conf
```

Add or modify the NTP server settings under `[Time]`. Use the NTP server of your choice. For example:

```ini
[Time]
NTP=0.au.pool.ntp.org
FallbackNTP=ntp.ubuntu.com
```

5. **Restart the service:**
    

```bash
sudo systemctl restart systemd-timesyncd
```

6. **Set the correct timezone:**
    

```bash
timedatectl list-timezones
sudo timedatectl set-timezone Australia/Melbourne

```

7. **Verify the timezone and synchronisation status:**
    

```bash
timedatectl status
```

With this, we would have the home lab server’s time synchronised automatically.

---

## 🔄 Step 2: Enable Automatic Security Updates

```bash
sudo apt install -y unattended-upgrades
sudo dpkg-reconfigure -plow unattended-upgrades
```

This configures Ubuntu to install security updates automatically.

---

## 🔐 Step 3: Harden SSH

1. **Edit SSH configuration:**
    

```bash
sudo nano /etc/ssh/sshd_config
```

Set the following options:

```ini
Port 7890               # Use a non-default port
PermitRootLogin no
MaxAuthTries 4
PasswordAuthentication no
PermitEmptyPasswords no
PubkeyAuthentication yes
AllowUsers your_username
```

2. **Optional**: Restrict SSH to a group:
    

```bash
sudo groupadd ssh_users
sudo usermod -aG ssh_users your_username
```

Then use `AllowGroups ssh_users` in the SSH config.

3. **Restart SSH:**
    

```bash
sudo systemctl restart ssh
```

---

## 🔥 Step 4: Configure Firewall with iptables

We have already installed Iptables in a step earlier. Now it is time to add some rules and configure it to harden our home lab server.

1. **View current rules:**
    

```bash
sudo iptables -L
```

2. **Set default policies:**
    

The first thing to do is to drop all incoming traffic by default and allow output communication. We will open up some ports and communication in upcoming steps. This will make the server reject any unwanted requests.

```bash
sudo iptables -P INPUT DROP
sudo iptables -P FORWARD DROP
sudo iptables -P OUTPUT ACCEPT
```

3. **Allow necessary traffic:**
    

```bash
# SSH (replace 7890 with your SSH port)
sudo iptables -A INPUT -p tcp --dport 7890 -j ACCEPT
sudo iptables -A INPUT -p tcp --dport 7890 -m conntrack --ctstate NEW -m recent --set
sudo iptables -A INPUT -p tcp --dport 7890 -m conntrack --ctstate NEW -m recent --update --seconds 60 --hitcount 4 -j DROP

# HTTP/HTTPS
sudo iptables -A INPUT -p tcp --dport 80 -j ACCEPT
sudo iptables -A INPUT -p tcp --dport 443 -j ACCEPT

# Loopback and existing connections
sudo iptables -A INPUT -i lo -j ACCEPT
sudo iptables -A OUTPUT -o lo -j ACCEPT
sudo iptables -A INPUT -m conntrack --ctstate ESTABLISHED,RELATED -j ACCEPT
```

We would be running some web applications in this server and default ports for HTTP and HTTPS communication should be left open.

Similarly, loop back traffic is generated between processes on the machine and is typically critical to the operation of the system. e.g. If we have a PostgreSQL DB and a web app, and they communicate on various ports. Since we are closing down a lot of traffic, it should not impact the running of these apps or the server.

4. **Save rules:**
    

These rules which we applied would not persist when we reboot the server. For them to stay, we need to ensure the rules persist after a reboot, save them to a file:

```bash
sudo mkdir -p /etc/iptables
sudo chown root:root /etc/iptables
sudo chmod 755 /etc/iptables
sudo iptables-save | sudo tee /etc/iptables/rules.v4
```

5. **Create systemd service to restore on boot:**
    

Now that the rules are saved, we need to reload it every time the computer boots up. Create a `systemd` service to load the rules automatically on boot.

```bash
sudo nano /etc/systemd/system/iptables.service
```

Paste:

```ini
[Unit]
Description=Packet Filtering Framework
DefaultDependencies=no
Before=network-pre.target
Wants=network-pre.target

[Service]
Type=oneshot
ExecStart=/sbin/iptables-restore /etc/iptables/rules.v4
ExecReload=/sbin/iptables-restore /etc/iptables/rules.v4
ExecStop=/sbin/iptables -F
RemainAfterExit=yes

[Install]
WantedBy=multi-user.target
```

6. **Enable and start service:**
    

```bash
sudo systemctl enable iptables
sudo systemctl start iptables
```

If the service is already running, restart it.

```bash
sudo systemctl restart iptables
sudo systemctl restart ssh
```

---

## 📋 Step 5: Install Security Tools

### Auditd - System Audit Logs

Auditd (Audit Daemon) is a powerful Linux auditing system that tracks and logs security-relevant events, such as file accesses and system calls, for detailed monitoring and analysis. Use `auditd` to monitor and log system events. Install it using the following:

```bash
sudo apt install -y auditd
sudo systemctl enable auditd
sudo systemctl start auditd
```

### Fail2Ban - Ban IPs on Failed Logins

Fail2Ban is a security tool that helps protect your server against brute force attacks. It works by monitoring log files for failed login attempts and other suspicious activities, and then temporarily or permanently bans IP addresses that exhibit these behaviours by modifying firewall rules. Install Fail2ban using the steps below to protect against brute-force attacks:

```bash
sudo apt install -y fail2ban
sudo systemctl enable fail2ban
sudo systemctl start fail2ban
```

### Lynis - Security Auditing

Run this after setting up all your services.

```bash
sudo apt install -y lynis
sudo lynis audit system
```

Optional packages (for passing more checks):

```bash
sudo apt install libpam-tmpdir apt-listchanges
```

## 📂 Step 6: Configure SFTP Access Securely

SFTP allows secure file transfer over SSH. If you're running your SSH server on a custom port (e.g., 7890), SFTP will use the same.

1\. **Add iptables rule for SFTP (same as SSH port)**

```bash
sudo iptables -A INPUT -p tcp --dport 7890 -j ACCEPT
sudo iptables-save | sudo tee /etc/iptables/rules.v4
```

2\. **Restart iptables**

```bash
sudo systemctl restart iptables
```

3. **Verify SSH is listening on the correct port:**
    

```bash
sudo netstat -tuln | grep 7890
# Expected output: something like
# tcp   0  0 0.0.0.0:7890   0.0.0.0:*   LISTEN
```

4. Test SFTP Client (e.g., FileZilla):
    

In the SFTP client (e.g., FileZilla), use the following configuration:

* Protocol: SFTP - SSH File Transfer Protocol
    
* Host: The home lab server's IP or hostname
    
* Port: `7890`
    
* User: your SSH login user
    
* Key file: Use your private key (e.g. `~/.ssh/id_ed25519`)
    

> 💡 If your key is in `.ppk` format (used by PuTTY), convert it to OpenSSH format before using with FileZilla.

---

With this, your server is now far more secure. In the next part, we’ll begin deploying containerised apps with Podman and Cockpit.

Stay tuned for **Part 4: Running Containers with Podman and Cockpit**!