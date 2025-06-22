---
title: "Part 4: Running Containers with Podman and Cockpit"
seoTitle: "Running Self-Hosted Containers with Podman and Cockpit on Ubuntu"
seoDescription: "Learn how to run Jellyfin and manage containers easily using Podman and Cockpit. Ideal for secure, local self-hosting on Ubuntu."
datePublished: Sun Jun 22 2025 14:00:49 GMT+0000 (Coordinated Universal Time)
cuid: cmc7qjope000902jo8bwug8ws
slug: part-4-running-containers-with-podman-and-cockpit
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1748867138770/5202bf36-a2cf-41bd-b2fe-258d72b9bfd1.png
ogImage: https://cdn.hashnode.com/res/hashnode/image/upload/v1748867238300/d947c89a-7e65-4aac-8a76-0e9794259ff8.png
tags: containers, podman, linux-server, cockpit, self-hosting

---

Welcome to Part 4 of the Ubuntu home lab series. Now that our server is secure, it's time to start running services in containers. We'll use **Podman** (a daemonless alternative to Docker) and **Cockpit** (a web-based UI) to make management easy. My strategy is to use containers whenever possible(e.g. PostgreSQL DB). If containerising is not possible, use a VM.

In this part, we'll:

* Install and configure Podman
    
* Set up Cockpit with container and VM support
    
* Run our first container (Jellyfin)
    
* Set up persistent volumes and startup scripts
    

---

## 🧱 Step 1: Install Podman

Podman is a daemonless container engine that allows you to run containers as non-root users, enhancing security and flexibility. It's designed to be compatible with Docker commands, making it easy to transition between the two. Additionally, Podman can pull and run Docker images, providing seamless integration with existing Docker workflows.

In a home lab setup, Podman offers the advantage of being managed easily through Cockpit, which we have already installed. This simplifies container management without needing additional tools like Portainer, which Docker requires for a web-based UI.

```bash
sudo apt update
sudo apt install -y podman podman-remote
```

You can confirm installation with:

```bash
podman --version
```

---

## 🌐 Step 2: Install Cockpit and Plugins

Cockpit is a web-based interface for managing Linux servers, providing a graphical dashboard to monitor and control various aspects of system administration, such as storage, network settings, and system performance. Cockpit allows you to install various plugins to manage VMs, Podman containers, View logs, manage users etc from a remote computer on the same network with ease.

```bash
sudo apt install -y \
  qemu-kvm libvirt-daemon-system libvirt-clients bridge-utils \
  cockpit cockpit-sosreport cockpit-machines \
  virt-manager cockpit-podman
```

Enable and start Cockpit:

```bash
sudo systemctl enable --now cockpit.socket
```

Cockpit will now be accessible at:

```bash
https://<your-server-ip>:9090
```

Use your server’s username/password to log in.

> 🔒 You can optionally configure SSL certificates for Cockpit.

Add firewall rule:

```bash
sudo iptables -A INPUT -p tcp --dport 9090 -j ACCEPT
sudo iptables-save | sudo tee /etc/iptables/rules.v4
```

Cockpit is now available using `https://<server-ip>:9090` from any machine in your home lab server’s network. Login with your server username and password.

---

## 🎬 Step 3: Run Your First Container (Jellyfin)

### 1\. Pull the image

I have chosen to install Jellyfin for the demonstration. Jellyfin is a free media system that allows you to manage and stream your media on your local network. You can follow the same method to spin up any container image.

```bash
podman pull docker.io/jellyfin/jellyfin:latest
```

Confirm that the image is pulled:

```bash
podman images
```

### 2\. Create directories

```bash
mkdir -p ~/.config/containers/jellyfin/config
mkdir -p ~/.config/containers/jellyfin/cache
mkdir -p ~/data/media
chmod -R 755 ~/.config/containers/jellyfin ~/data/media
```

### 3\. Create persistent volumes

A Podman volume is a section of the host file system that is attached to a running container. It allows data to persist and be shared between containers.

```bash
podman volume create --opt device=$HOME/.config/containers/jellyfin/config --opt type=none --opt o=bind jellyfin-config
podman volume create --opt device=$HOME/.config/containers/jellyfin/cache --opt type=none --opt o=bind jellyfin-cache
podman volume create --opt device=$HOME/data/media --opt type=none --opt o=bind jellyfin-media
```

Creating the volume and passing it is always recommended as it helps to identify the volume when we list using `podman volume ls` and remove using `podman volume rm <imagename>`. If we don’t create explicitly, podman creates automatically and the names would not be easily identifiable.

Use `podman volume rm <volume name>` to delete a volume if required.

### 4\. Run the container

```bash
podman run -d \
  --name jellyfin \
  --restart=always \
  -p 8096:8096 \
  -p 8920:8920 \
  -v jellyfin-config:/config \
  -v jellyfin-cache:/cache \
  -v jellyfin-media:/media \
  jellyfin/jellyfin:latest
```

### 5\. Allow traffic

```bash
sudo iptables -A INPUT -p tcp --dport 8096 -j ACCEPT
sudo iptables -A INPUT -p tcp --dport 8920 -j ACCEPT
sudo iptables-save | sudo tee /etc/iptables/rules.v4
```

This will create a Podman container with the name `jellyfin` which runs on your new server. You can upload all the media to `~/data/media` folder and organise them in Jellyfin.

Access Jellyfin:

```bash
http://<your-server-ip>:8096
```

*If the Jellyfin wizard does not come up, try accessing* `http://<server-ip>:8096/web/index.html#!/wizardstart.html`*.*

---

## 🔁 Step 4: Auto-start Containers on Boot

Podman is daemonless, so containers won’t auto-start after reboot by default. Let’s fix that with a simple systemd service.

### 1\. Create a startup script

```bash
mkdir -p ~/.config/containers
nano ~/.config/containers/start.sh
```

Example content:

```bash
#!/bin/bash
podman start jellyfin
```

Make it executable:

```bash
chmod +x ~/.config/containers/start.sh
```

### 2\. Create a systemd service

```bash
sudo nano /etc/systemd/system/start-containers.service
```

Paste:

```ini
[Unit]
Description=Start Podman Containers
After=network.target

[Service]
Environment="XDG_RUNTIME_DIR=/run/user/1000"
ExecStartPre=/bin/sleep 10
ExecStart=/bin/bash /home/<your-username>/.config/containers/start.sh
User=<your-username>
Restart=always

[Install]
WantedBy=default.target
```

> Replace `<your-username>` accordingly.

Enable and start the service:

```bash
sudo systemctl enable start-containers.service
sudo systemctl start start-containers.service
```

---

In the next part, we’ll go further with multi-container pods using Podman. We’ll set up PostgreSQL + pgAdmin, Node-RED, and Homarr for managing your homelab.

Stay tuned for **Part 5: Advanced Containers – pgAdmin, Node-RED, and Homarr**!