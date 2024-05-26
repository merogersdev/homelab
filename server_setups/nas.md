# NAS Server

## Summary

Main file server with high-performing ZFS storage and Plex Media Server. Options for Docker containers and VMs.

## Specifications

- OS: TrueNAS Scale 24.04.0
- CPU: Intel Core 2 Quad Q6600@2.4GHz
- Memory: 8GB
- Disks:
  - Main: 128GB Corsair SSD
  - Storage: 2x 4TB WD 5400RPM HDDs

## Network

- IP: 10.10.1.10
- Gateway: 10.10.1.1
- DNS Server: 10.10.1.1

## Storage

- Pool: zPool
- Data VDEVs: 1x Mirror 3.64TiB

## Datasets

- Name: Shared
- Roles: SMB
- Permissions: User - apps, Allow | Modify

## Apps

### Plex

- Application Name: Plex
- Version: 2.0.6
- Timezone: America/Regina
- Image: Plex Official Image
- User ID: 568
- Group ID: 568
- Additional Storage
  - Host Path
  - Mount Path: /Media
  - Host Path Configuration: Enable ACL
  - Host Path: /mnt/zPool/Shared/Media
  - ACL Entries:
    - ID Type: Entry is for a USER
    - ID: 568
    - Access: Full Control
    - ACL Options: Force Flag
