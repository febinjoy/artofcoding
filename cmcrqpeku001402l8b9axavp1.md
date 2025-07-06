---
title: "Part 6: Nginx Reverse Proxy for Clean URLs"
seoTitle: "Simplify Home Lab Access with Nginx Reverse Proxy on Ubuntu"
seoDescription: "Use Nginx to reverse proxy and access your home lab apps with clean URLs like http://jellyfin.myserver.home. Complete config included."
datePublished: Sun Jul 06 2025 14:00:40 GMT+0000 (Coordinated Universal Time)
cuid: cmcrqpeku001402l8b9axavp1
slug: part-6-nginx-reverse-proxy-for-clean-urls
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1748867162942/b5ab10fa-50fd-41d8-84ad-a19a97b360a8.png
ogImage: https://cdn.hashnode.com/res/hashnode/image/upload/v1748867556888/dee849e8-9f06-444c-8657-915dad7b4258.png
tags: dns, nginx, reverse-proxy, self-hosting, clean-urls

---

Welcome to Part 6 of the Ubuntu home lab series. Now that we have several apps running on different ports, it’s time to simplify access using **Nginx** as a reverse proxy. This lets us use URLs like `http://jellyfin.myserver.home` instead of IP:port combos.

> 🧠 Tip: Use your router or hosts file to map custom domains (e.g., `myserver.home`) to your server’s IP.

---

## 🌐 Step 1: Install Nginx

```bash
sudo apt update
sudo apt install -y nginx
```

Check if it’s running:

```bash
sudo systemctl status nginx
```

I decided to install Nginx locally on the server as it would be able to access port 80 of my server and route traffic to various apps/containers.

---

## 📁 Step 2: Set Up Config Directory

```bash
sudo mkdir -p /etc/nginx/conf.d
```

---

## 📝 Step 3: Create Reverse Proxy Config

```bash
sudo nano /etc/nginx/conf.d/myserver.conf
```

Add entries like the following for each service:

### Jellyfin

```nginx
server {
  listen 80;
  server_name jellyfin.myserver.home;

  location / {
    proxy_pass http://<your-server-ip>:8096;
    proxy_set_header Host $host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header X-Forwarded-Proto $scheme;
  }
}
```

This will map [`http://jellyfin.myserver.home`](http://jellyfin.myserver.home) to `http://<your-server-ip>:8096`

Repeat similar blocks for other apps:

### PostgreSQL (pgAdmin)

```nginx
server {
  listen 80;
  server_name postgres.myserver.home;

  location / {
    proxy_pass http://<your-server-ip>:9876;
    proxy_set_header Host $host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header X-Forwarded-Proto $scheme;
  }
}
```

### Node-RED

```nginx
server {
  listen 80;
  server_name nodered.myserver.home;

  location / {
    proxy_pass http://<your-server-ip>:1880;
    proxy_set_header Host $host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header X-Forwarded-Proto $scheme;
  }
}
```

### Homarr

```nginx
server {
  listen 80;
  server_name dashboard.myserver.home;

  location / {
    proxy_pass http://<your-server-ip>:7575;
    proxy_set_header Host $host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header X-Forwarded-Proto $scheme;
  }
}
```

---

## 🔍 Step 4: Test and Apply Nginx Config

### 1\. Test configuration:

```bash
sudo nginx -t
```

### 2\. Restart Nginx:

```bash
sudo systemctl restart nginx
```

---

## 🧭 Step 5: Add Hostname Mapping (on client machines)

Edit `/etc/hosts` (on Linux/macOS) or `C:\Windows\System32\drivers\etc\hosts` (on Windows) and add:

```bash
192.168.1.100 jellyfin.myserver.home postgres.myserver.home nodered.myserver.home dashboard.myserver.home
```

> Replace `192.168.1.100` with your server’s actual IP.

Now you can access:

* `http://jellyfin.myserver.home`
    
* `http://postgres.myserver.home`
    
* `http://nodered.myserver.home`
    
* `http://dashboard.myserver.home`
    

---

## ✅ Final Notes

Your home lab is now:

* Secure and hardened
    
* Running in containers
    
* Easily accessible via custom URLs
    

You can keep extending it with apps like Kavita, FreshRSS, Calibre-Web, or even GitLab. Just follow the same Podman + volume + Nginx proxy pattern.

Thanks for following along! 🧑‍💻

> Feel free to share your thoughts or improvements at [artofcoding.dev](https://artofcoding.dev)!