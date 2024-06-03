# My HomeLab Documentation

## Summary

Business-class SDN network with Mesh support, VLANs, NAS/File Server, Media Server, Network Controller, and Ad Blocker.

## Network At a Glance

### Core Services

- TP-Link Omada Network Controller (Docker)
- AdGuard Home DNS Server & Ad Block (Docker)
- TrueNAS Scale NAS/File Server (Bare-Metal)
- Plex Media Server (TrueNAS Docker App)

### Main Network Configuration

- VLAN 1: Management VLAN (Infrastructure)
- VLAN 10: Building VLAN (Home Network)
  - SSID: Building, 5GHz
  - SSID: Maclennan, 2.4GHz
- VLAN 20: Guest VLAN (Isolated Guest Network)
  - SSID: MaxWiFi Guest, 5GHz & 2.4GHz
- VLAN 30: Lab VLAN (HomeLab Test Network)
  - SSID: MaxWiFi, 5GHz & 2.4GHz

### Individual Configurations

- [IP Address Assignment](ip.addresses.md)
- [Network Controller](config/controller.md)
- [NAS/File Server](config/nas.md)
- [Docker Server](config/docker.md)

### Network Maps

- [Physical Network Map](maps/physical.pdf)
- [Logical Network Map](maps/logical.pdf)

### Next Up

- [Hypervisor Configuration (TO DO)](config/hypervisor.md)
- Disaster Recovery & Backup (TO DO)
- Floor Plan (TO DO)
- Monitoring (TO DO)

## Pain Points

- Building floors and walls are thick concrete. No hardwired connections are possible, except to the main router.
- Single router offers adequate coverage for one floor, but minimal to no coverage for the other 3 as well as outside.
- Very limited budget, and will have to utilize hardware that I have on-hand.
- Switch cannot be adopted by Omada Controller. Has to be configured as standalone. Known issue.

## Areas to improve

- Wired backhaul to each AP would improve performance.
- APs are previous-generation (WiFi 5/AC), and will need to be upgraded later.
- NAS system would benefit with an upgrade.
