# Switch

## Summary

TP-Link TL-SG108E managed switch, in standalone mode.

## Configuration

### System

#### System Info

- Device Description: MainSwitch
- IP Address: 10.10.1.6
- Subnet Mask: 255.255.255.0
- Default Gateway: 10.10.1.1
- Firmware Version: 1.0.0 Build 20230218 Rel.50633
- Hardware Version: TL-SG108E 6.0

### VLAN

#### 802.1Q VLAN

| VLAN ID | VLAN Name  | Member Ports | Tagged Ports | Untagged Ports |
| ------- | ---------- | ------------ | ------------ | -------------- |
| 1       | Management | 1-4          | 1            | 2-4            |
| 10      | Building   | 1,5          | 1            | 5              |
| 20      | Guest      | 1            | 1            |                |
| 30      | Lab        | 1,6-8        | 1            | 6-8            |

#### 802.1Q PVID

| Port 1 | Port 2 | Port 3 | Port 4 | Port 5 | Port 6 | Port 7 | Port 8 |
| ------ | ------ | ------ | ------ | ------ | ------ | ------ | ------ |
| 1      | 1      | 1      | 1      | 10     | 30     | 30     | 30     |
