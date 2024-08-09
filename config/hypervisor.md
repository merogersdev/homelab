# Hypervisor (TO DO)

## Proxmox

- Install Proxmox and log into web interface http://10.10.1.10:8006

### Bridge - Create Linux Bridge

- Name: vmbr0
- IPv4/CIDR: 10.10.1.10/24
- Gateway (IPv4): 10.10.1.1
- Bridge ports: enp0s25

### Create ZFS Pool

- Name: zPool
- RAID Level: Mirror
- Compression: on
- /dev/sdb and /dev/sdc checked

### Download CT Templates

- debian-12-standard
- ubuntu-24.04-standard

### NAS VM

#### General

- Node: pve
- VM ID: 111
- Name: NAS

#### OS

- Storage: Local
- ISO Image: debian-12.5.x.xxxxx.iso
- Type: Linux
- Version: 6.x - 2.6 Kernel

#### System

- Graphic card: Default
- Machine: Default (i440fx)
- BIOS: Default (SeaBIOS)
- SCSI Controller: VirtIO SCSI single

#### Disks

- Storage: local
- Disk Size (GiB): 8

#### CPU

- Sockets: 1
- Cores: 1
- Type: host

#### Memory

- Memory (MiB): 2048

#### Network

- Bridge: vmbr0
- Model: VirtIO (paravirtualized)

#### Add: Hard Disk

- Storage: zPool
- Disk Size (GiB): 3500

### Install Debian 12

- Select locale settings
- Configure Network Manually
  - IP Address: 10.10.1.11
  - Subnet mask: 255.255.255.0
  - Gateway: 10.10.1.1
  - Name server addresses: 10.10.1.1
- Hostname: nas.obsidian.local
- Root Password: blank
- Set user (sudo) account
- Configure the Clock: Saskatchewan
- Partition disks: Guided - use entire disk and set up lvm
- Select 8GB disk (smaller one)
- All Files in one partition (separate /home partition not recommended with small boot drive)
- No additional Media
- Debian archive mirror country: canada
- Debian archive mirror: deb.debian.org
- HTTP Proxy: None
- Software Selection
  - Debian Desktop Environment: Unchecked
  - GNOME: Unchecked
  - SSH Server: Checked
- Install GRUB Boot Loader: yes
- Device for bootloader installation: (small drive)

### Setup Samba and HDD mount

1. SSH in to VM as non-root user
2. Update system
   `sudo apt update && sudo apt upgrade -y`
3. Add base packages
   `sudo apt install samba vim curl parted -y`
4. Partition and format storage drive

   ```
   sudo parted /dev/sdb

   mklabel gpt
   mkpart primary ext4 1MiB 100%
   quit

   sudo mkfs.ext4 /dev/sdb1
   ```

5. Determine UUID of /dev/sdb1
   `sudo blkid`
6. Edit fstab file, and append mountpoint
   `UUID=xxxx-xxxx-xxxx-xxxx-xxxx /mnt/Storage ext4 defaults,nofail 0 0`
7. Reboot, SSH back in and check drive is mounted at /mnt/Storage
   `df -h`
8. Create shared folder
   `sudo mkdir -P /mnt/Storage/Shared`
9. Create samba group
   `sudo groupadd samba`
10. Change group of Shared folder
    `sudo chown :samba -R /mnt/Storage/Shared`
11. Add group write permissions to folder
    `sudo chhmod g+w -R /mnt/Storage/Shared`
12. Add Samba group to user
    `sudo usermod -aG samba user`
13. Set samba user password
    `sudo smbpasswd -a user`
14. Edit /etc/samba/smb.conf, and append the shared folder

```
[Shared]
  comment = Shared Folder
  path = /mnt/Storage/Shared
  browseable = yes
  guest ok = no
  writeable = yes
  read only = no
  valid users = @samba
```

15. Restart samba
    `sudos systemctl restart smbd`

### Setup Plex

1. Make Downloads Folder
   `cd ~ && mkdir Downloads && cd Downloads`
2. Download Plex Media Server
   `curl https://downloads.plex.tv/plex-media-server-new/1.40.2.8395-c67dce28e/debian/plexmediaserver_1.40.2.8395-c67dce28e_amd64.deb -O plexmediaserver_1.40.2.8395-c67dce28e_amd64.deb`
3. Install Plex Media Server
   `sudo dpkg -i plexmediaserver_1.40.2.8395-c67dce28e_amd64.deb`
4. Log in to web interface at http://10.10.1.10:32400/web and set up plex

### Setup Firewall

1. Install UFW
   `sudo apt install ufw`
2. Allow SSH
   `sudo ufw allow ssh`
3. Allow Samba
   `sudo ufw allow samba`
4. Create Plex profile at /etc/ufw/applications.d/plexmediaserver

```
[plexmediaserver]
title=Plex Media Server (Standard)
description=The Plex Media Server
ports=32400/tcp|3005/tcp|5353/udp|8324/tcp|32410:32414/udp

[plexmediaserver-dlna]
title=Plex Media Server (DLNA)
description=The Plex Media Server (additional DLNA capability only)
ports=1900/udp|32469/tcp

[plexmediaserver-all]
title=Plex Media Server (Standard + DLNA)
description=The Plex Media Server (with additional DLNA capability)
ports=32400/tcp|3005/tcp|5353/udp|8324/tcp|32410:32414/udp|1900/udp|32469/tcp
```

5. Add Plex profile to UFW
   `sudo ufw app update plexmediaserver`
6. Add Plex to UFW
   `sudo ufw allow plexmediaserver-all`
7. Enable UFW
   `sudo ufw enable`
