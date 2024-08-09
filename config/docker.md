# Docker Server

## Summary

Primary docker server on a RaspberryPi, hosting core network services including the main controller and Ad Block.

## Specifications

- RaspberryPi 4 Model B, 4GB
- OS: RaspberryPi OS 64-bit Lite (Debian 12)
- Hostname: pidoc
- IP Address: 10.10.1.2/24
- Gateway: 10.10.1.1
- DNS Server: 10.10.1.1

## Core Services

- Docker Community Edition
- AdGuard Home
- TP-Link Omada Controller
- Portainer
- HomePage Dashboard
- UFW Firewall
- OpenSSH Server

## Docker Container Configuration

1. Omada Controller

docker-compose.yml

```
services:
  omada-controller:
    container_name: omada-controller
    image: mbentley/omada-controller:latest
    restart: unless-stopped
    ulimits:
      nofile:
        soft: 4096
        hard: 8192
    stop_grace_period: 60s
    network_mode: host
    environment:
      - PUID=508
      - PGID=508
      - MANAGE_HTTP_PORT=8088
      - MANAGE_HTTPS_PORT=8043
      - PORTAL_HTTP_PORT=8088
      - PORTAL_HTTPS_PORT=8843
      - PORT_APP_DISCOVERY=27001
      - PORT_ADOPT_V1=29812
      - PORT_UPGRADE_V1=29813
      - PORT_MANAGER_V1=29811
      - PORT_MANAGER_V2=29814
      - PORT_DISCOVERY=29810
      - PORT_TRANSFER_V2=29815
      - PORT_RTTY=29816
      - SHOW_SERVER_LOGS=true
      - SHOW_MONGODB_LOGS=false
      - SSL_CERT_NAME=tls.crt
      - SSL_KEY_NAME=tls.key
      - TZ=America/Regina
    volumes:
      - ./data:/opt/tplink/EAPController/data
      - ./logs:/opt/tplink/EAPController/logs

volumes:
  data:
  logs:
```

2. AdGuard Home

docker-compose.yml

```
services:
  adguardhome:
    image: adguard/adguardhome
    container_name: adguardhome
    ports:
      - 53:53/tcp
      - 53:53/udp
      - 784:784/udp
      - 853:853/tcp
      - 3000:3000/tcp
      - 80:80/tcp
      - 443:443/tcp
    volumes:
      - ./workdir:/opt/adguardhome/work
      - ./confdir:/opt/adguardhome/conf
    restart: unless-stopped
```

3. Portainer

docker-compose.yml

```
services:
  portainer:
    image: portainer/portainer-ce:latest
    ports:
      - 9443:9443
    volumes:
      - ./data:/data
      - /var/run/docker.sock:/var/run/docker.sock
    restart: unless-stopped
volumes:
  data:
```

4. HomePage

```
services:
  homepage:
    image: ghcr.io/gethomepage/homepage:latest
    container_name: homepage
    restart: unless-stopped
    env_file: "./.env"
    ports:
      - 3001:3000
    volumes:
      - ./config:/app/config # Make sure your local config directory exists
      - /var/run/docker.sock:/var/run/docker.sock #
```

## UFW Configuration

### Allow

- SSH (22)
- Omada (Profile)
- AdGuard (Profile)
- Portainer (9000)
- HomePage (3001)
- Nginx Proxy Manager (81)

### Custom Profiles

1. Omada

/etc/ufw/applications.d/omada

```
[Omada]
title=TP-Link Omada Controller
description=Omada Network Controlller
ports=8088/tcp|8043/tcp|8843/tcp|27001/udp|29810/udp|27217/tcp|29810/udp|29811:29816/tcp
```

2. AdGuard

/etc/ufw/applications.d/adguard

```
[AdGuard]
title=AdGuard Home
description=AdGuard Home DNS Server and Ad Blocker
ports=53/tcp|53/udp|784/udp|853/tcp|3000/tcp|80/tcp|443/tcp
```
