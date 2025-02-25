# RustDesk Self-Hosted Server Setup Guide

This guide provides a step-by-step procedure to set up and configure RustDesk self-hosted servers (HBBS & HBBR) correctly. Following these steps will ensure that your setup works 100% from the beginning without issues.

## 🛠️ Step 1: Set Up a Server for RustDesk

You need a VPS, cloud server, or on-premise machine with:

- Ubuntu 20.04 / 22.04 / 24.04 (Recommended)
- A public IP address (if clients will connect from outside)
- Docker installed (RustDesk HBBS/HBBR runs in Docker)

### Install Docker & Docker-Compose

```bash
sudo apt update && sudo apt upgrade -y
sudo apt install docker.io docker-compose -y
sudo systemctl enable --now docker
```

## 🌍 Step 2: Set Up RustDesk HBBS (Rendezvous) & HBBR (Relay)

We will use Docker-Compose to set up both servers.

### Create a Working Directory

```bash
mkdir -p ~/rustdesk-server && cd ~/rustdesk-server
```

### Create a `docker-compose.yml` File

```bash
nano docker-compose.yml
```

Paste the following content:

```yaml
version: '3'
services:
  hbbs:
    image: rustdesk/rustdesk-server:latest
    container_name: hbbs
    restart: always
    network_mode: "host"
    volumes:
      - ./data:/root
    command: >
      hbbs -r <YOUR_SERVER_IP>:21117

  hbbr:
    image: rustdesk/rustdesk-server:latest
    container_name: hbbr
    restart: always
    network_mode: "host"
    volumes:
      - ./data:/root
    command: >
      hbbr
```

🔹 Replace `<YOUR_SERVER_IP>` with your actual public IP address or local IP if running on a LAN.

## 🚀 Step 3: Start RustDesk Servers

Run the following command:

```bash
sudo docker-compose up -d
```

This will:

- Download and run HBBS (Rendezvous) on ports 21115, 21116, and 21118.
- Download and run HBBR (Relay) on ports 21117 and 21119.

## 🔑 Step 4: Get the Server Key

Run:

```bash
sudo docker logs hbbs --tail 50
```

Look for a line like this:

```
[INFO] Key: 5opIdjZJsxPbXcs7fmUKMzAkoPsh8Pbcz5yBq1r9XRM=
```

🔹 This is the key you need for RustDesk clients.

## 🛠️ Step 5: Open Required Ports in Firewall

Run:

```bash
sudo ufw allow 21115/tcp
sudo ufw allow 21116/tcp
sudo ufw allow 21116/udp
sudo ufw allow 21117/tcp
sudo ufw allow 21118/tcp
sudo ufw allow 21119/tcp
sudo ufw reload
```

🔹 This ensures all required TCP/UDP ports are open.

## 💻 Step 6: Configure RustDesk Clients

Now, on the RustDesk Client, go to:

- Settings → Network
- Check "Use Custom Server"
- Enter your server IP in both fields:
  - Rendezvous Server: `<YOUR_SERVER_IP>:21115`
  - Relay Server: `<YOUR_SERVER_IP>:21117`
- Enter the Key from Step 4.

## ✅ Step 7: Test Remote Connection

- Install RustDesk on another machine (Windows/Linux/Mac).
- Enter the RustDesk ID of the target machine.
- Connect and control the machine remotely!

## 🛠️ Step 8: Manage & Troubleshoot

If you face issues:

### Restart servers:

```bash
sudo docker-compose down
sudo docker-compose up -d
```

### Check logs:

```bash
sudo docker logs hbbs --tail 50
sudo docker logs hbbr --tail 50
```

### Ensure firewall/ports are open:

```bash
sudo ufw status
```

## 🎯 Conclusion

Following this step-by-step guide, your RustDesk self-hosted server should work flawlessly from the start.

🚀 Let me know if you need help!
