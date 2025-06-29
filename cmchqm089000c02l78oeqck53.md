---
title: "Part 5: Advanced Containers – pgAdmin, Node-RED, and Homarr"
seoTitle: "Deploying PostgreSQL, Node-RED, and Homarr in Containers with Podman"
seoDescription: "Set up advanced services like pgAdmin, PostgreSQL, Node-RED, and Homarr in Podman containers. Clean, modular, and secure."
datePublished: Sun Jun 29 2025 14:00:19 GMT+0000 (Coordinated Universal Time)
cuid: cmchqm089000c02l78oeqck53
slug: part-5-advanced-containers-pgadmin-node-red-and-homarr
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1748867150249/318407dc-3e07-4d89-9021-48829a374b67.png
ogImage: https://cdn.hashnode.com/res/hashnode/image/upload/v1748867314060/56c3917e-3b54-44dc-93f0-2de79954ef41.png
tags: postgresql, postgres, containers, node-red, podman, pgadmin, homarr

---

Welcome to Part 5 of the Ubuntu home lab series. This part expands on our Podman setup by deploying more advanced services inside containers. We’ll run PostgreSQL with pgAdmin in a shared Pod, Node-RED for workflows, and Homarr for a beautiful home lab dashboard.

---

## 🐘 Step 1: Set Up PostgreSQL + pgAdmin in a Pod

Podman supports pods, similar to Kubernetes. Containers in a pod share network and can easily talk to each other.

### 1\. Create the pod

```bash
podman pod create --name postgre-sql -p 9876:80
```

This exposes port 80 of the pod to port 9876 on the host.

### 2\. Set up pgAdmin

```bash
mkdir -p ~/.config/containers/pgadmin
chmod -R 755 ~/.config/containers/pgadmin

podman volume create --opt device=$HOME/.config/containers/pgadmin \
  --opt type=none --opt o=bind pgadmin-volume

podman run --pod postgre-sql \
  -e 'PGADMIN_DEFAULT_EMAIL=youradmin@yourdomain.com' \
  -e 'PGADMIN_DEFAULT_PASSWORD=passWoRd' \
  -v pgadmin-volume:/var/lib/pgadmin \
  --name pgadmin \
  -d docker.io/dpage/pgadmin4:latest
```

This will pull the pgAdmin docker image and create a podman container in the pod we created earlier. You can use the cockpit to view the status.

### 3\. Set up PostgreSQL

```bash
mkdir -p ~/.config/containers/postgres
chmod -R 755 ~/.config/containers/postgres

podman volume create --opt device=$HOME/.config/containers/postgres \
  --opt type=none --opt o=bind postgres-volume

podman run --name postgredb \
  --pod postgre-sql \
  -d -e POSTGRES_USER=admin \
  -e POSTGRES_PASSWORD=passWoRd \
  -v postgres-volume:/var/lib/postgresql/data:rw,z \
  docker.io/library/postgres:latest
```

This will pull the pgAdmin docker image and create a podman container in the pod we created earlier. You can use the cockpit to view the status.

### 4\. Allow access

```bash
sudo iptables -A INPUT -p tcp --dport 9876 -j ACCEPT
sudo iptables-save | sudo tee /etc/iptables/rules.v4
```

### 5\. Access and configure

* Access the pgAdmin web interface by navigating to : `http://<your-server-ip>:9876`
    
* In pgAdmin, add a new server:
    
    * **Name**: `mypgdb`
        
    * **Host**: `0.0.0.0`
        
    * **Username/Password**: match POSTGRES\_USER and POSTGRES\_PASSWORD
        

### 6\. Enable auto-start

Edit your startup script:

```bash
echo "podman pod start postgre-sql" >> ~/.config/containers/start.sh
```

---

## 🔗 Step 2: Run Node-RED

### 1\. Create directory and volume

```bash
mkdir -p ~/.config/containers/nodered
chmod -R 755 ~/.config/containers/nodered

podman volume create --opt device=$HOME/.config/containers/nodered \
  --opt type=none --opt o=bind nodered-data
```

### 2\. Run the container

```bash
podman run -d \
  -p 1880:1880 \
  --name nodered \
  -v nodered-data:/data \
  --restart always \
  docker.io/nodered/node-red:latest
```

### 3\. Allow access

```bash
sudo iptables -A INPUT -p tcp --dport 1880 -j ACCEPT
sudo iptables-save | sudo tee /etc/iptables/rules.v4
```

### 4\. Enable auto-start

```bash
echo "podman start nodered" >> ~/.config/containers/start.sh
```

Access: `http://<your-server-ip>:1880`

---

## 🧭 Step 3: Set Up Homarr Dashboard

Homarr is a wonderful dashboard that will make your home lab server more accessible and easy to use. It provides a beautiful UI to access all your self-hosted apps.

### 1\. Create directories

```bash
mkdir -p ~/.config/containers/homarr/configs
mkdir -p ~/.config/containers/homarr/icons
mkdir -p ~/.config/containers/homarr/data
chmod -R 755 ~/.config/containers/homarr
```

### 2\. Create volumes

```bash
podman volume create --opt device=$HOME/.config/containers/homarr/configs --opt type=none --opt o=bind homarr-configs
podman volume create --opt device=$HOME/.config/containers/homarr/icons --opt type=none --opt o=bind homarr-icons
podman volume create --opt device=$HOME/.config/containers/homarr/data --opt type=none --opt o=bind homarr-data
```

### 3\. Run the container

```bash
podman run -d \
  -p 7575:7575 \
  --name homarr \
  -v homarr-configs:/app/data/configs \
  -v homarr-icons:/app/public/icons \
  -v homarr-data:/data \
  --restart unless-stopped \
  -e DEFAULT_COLOR_SCHEME=dark \
  ghcr.io/ajnart/homarr:latest
```

### 4\. Allow access

```bash
sudo iptables -A INPUT -p tcp --dport 7575 -j ACCEPT
sudo iptables-save | sudo tee /etc/iptables/rules.v4
```

### 5\. Enable auto-start

```bash
echo "podman start homarr" >> ~/.config/containers/start.sh
```

Access: `http://<your-server-ip>:7575`

---

Coming up next in Part 6: we’ll simplify access to all these services using Nginx as a reverse proxy so you can use friendly URLs like [`http://jellyfin.myserver.home`](http://jellyfin.myserver.home) instead of remembering port numbers.

Stay tuned for **Part 6: Nginx Reverse Proxy for Clean URLs**!