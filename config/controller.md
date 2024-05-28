# TP-Link Omada Controller Configuration

# Summary

SDN network for homelab. All settings not mentioned here are as default.

# Devices

## Router

- TP-Link ER-605 V2 x 1
- Ubiquiti EdgeRouter X (Backup - Standalone)

## Access Points

- TP-Link EAP225 V3 x 1
- TP-Link EAP225 V4 x 2

## Switches

- TP-Link SG-108E8 Port Gigabit Managed Switch (Standalone)
- TrendNet TEG-S80g 8 Port Gigabit Switch (Backup)

# Site

## Site Configuration

- Site Name: HomeLab
- Country/Region: Canada
- Time Zone: (UTC-06:00) Saskatchewan
- Application Scenario: Home
- Network Time Protocol: Enabled
- Server Address: ca.pool.ntp.org

## Wired Networks

### Internet

- Description: Rogers/Shaw
- IPv4 Connection Type: Dynamic IP

### LAN (VLANs)

#### Lab

- Name: Lab
- Purpose: Interface
- LAN Interfaces: All
- VLAN: 1
- Gateway/Subnet: 10.10.1.1/24
- DHCP Server: Enable
- DHCP Range: 10.10.1.100 - 10.10.1.199
- DNS Server: Auto

#### Building

- Name: Building
- Purpose: Interface
- LAN Interfaces: All
- VLAN: 10
- Gateway/Subnet: 10.10.10.1/24
- DHCP Server: Enable
- DHCP Range: 10.10.10.100 - 10.100.10.199
- DNS Server: Auto

#### Guest

- Name: Guest
- Purpose: Interface
- LAN Interfaces: All
- VLAN: 10
- Gateway/Subnet: 10.10.30.1/24
- DHCP Server: Enable
- DHCP Range: 10.10.30.100 - 10.100.30.199
- DNS Server: 1.1.1.3, 1.0.0.3

## Wireless Networks

### MaxWiFi

- Network Name (SSID): MaxWiFi
- Band: 2.4 GHz, 5 GHz
- Security: WPA-Personal

### MaxWiFi Guest

- Network Name (SSID): MaxWiFi Guest
- Band: 2.4 GHz, 5 GHz
- Security: WPA-Personal
- VLAN: 20
- Client Rate Limit Profile: Custom
- Client Download Limit: Enabled, 10 Mbps
- Client Upload Limit: Enabled, 5 Mbps

### Building

- Network Name (SSID): Building
- Band: 5 GHz
- Security: WPA-Personal
- VLAN: 10

### Maclennan

- Network Name (SSID): Maclennan
- Band: 2.4 GHz
- Security: WPA-Personal
- VLAN: 10

## Network Security

### ACL

#### Gateway ACL

##### Isolate Building

- Status: Enabled
- Direction LAN -> LAN
- Policy: Deny
- Protocols: All
- Source Type: Network -> Building
- Destination Type: Network -> Lab, Guest

##### Isolate Guest

- Status: Enabled
- Direction LAN -> LAN
- Policy: Deny
- Protocols: All
- Source Type: Network -> Guest
- Destination Type: Network -> Lab, Building

##### Deny Access to GW from Guest

- Status: Enabled
- Direction LAN -> LAN
- Policy: Deny
- Protocols: TCP
- Source Type: Network -> Guest
- Destination Type: Gateway Management Page

##### Deny Access to GW from Building

- Status: Enabled
- Direction LAN -> LAN
- Policy: Deny
- Protocols: TCP
- Source Type: Network -> Building
- Destination Type: Gateway Management Page

#### EAP ACL

##### Isolate Building

- Status: Enabled
- Policy: Deny
- Protocols: All
- Source Type: Network -> Building
- Destination Type: Network -> Lab, Guest

##### Isolate Guest

- Status: Enabled
- Policy: Deny
- Protocols: All
- Source Type: Network -> Guest
- Destination Type: Network -> Lab, Building

## Services

### SNMP

- SNMPv3: Enabled
- SNMPv1 & SNMPv2c: Disabled

### Upgrade Schedule

- Name: Upgrade Once a Month
- Status: Enabled
- Type: Repeat
- Occurence: Every Month on 1 at 00:00

### DNS Cache

- Enable Dns Cache: Enabled
- TTL: 86400s
