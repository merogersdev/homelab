# HomeLab VM Server

## Setup

1. Download Proxmox VE 8.2, and use a program like [Rufus](https://rufus.ie/en/) or [BalenaEtcher](https://etcher.balena.io/) to write ISO to flash drive.
2. Boot server and from the boot menu, select the flash drive.
3. Select Instal Proxmox VE (Graphical)
4. Accept EULA
5. Make sure your correct main HDD is selected for Target Harddisk
6. Set Country, Timezone, and Keyboard Layout
7. Set Root password and main email
8. Set Hostname: pve.obsidian.local, IP Address: 10.10.10.10/24, Gateway: 10.10.10.1, DNS Server: 10.10.10.1
9. Review summary, install, and reboot when done

## Upload ISOs

1. Download a copy of Debian 12 to your local machine
2. In Proxmox UI, select local (pve) in tree and select ISO Images and Upload Debian ISO, along with any other OS images that will be used for VMs.

## Setup VMs

### 111 - NAS

- OS: Debian 12
- Hostname: nas.obsidian.local
- Desktop Environment: none
- Additional Packages: openssh, samba, ufw
- IP: 10.10.10.11

#### VM Setup

1. Create VM in Proxmox
2. General: Set name as NAS
3. OS: Select Debian ISO image
4. System: Leave as defaults
5. Disks: Set Disk size (GiB) to 8
6. CPU: Sockets: 1, Cores: 1, Type: Host
7. Memory: Memory (MiB): 2048
8. Network: Leave as defaults
9. Confirm

#### Debian Installation

1. Start VM and select Install from menu
2. Select language, location and keyboard
3. Interrupt IP address autodetection, and opt for 'configure the network manually' to set static IP
4. Set the IP 10.10.10.11, Netmask: 255.255.255.0, Gateway: 10.10.10.1, Name server addresses: 10.10.10.1
5. Set hostname: eg. nas.obsidian.local
6. For root password, leave blank (disables root account)
7. Set admin user credentials
8. Configure clock for correct timezone
9. Partition Disks: Guided - use entire disk, select only HDD, All files in one partition, Finish partitioning and write changes to disk, Yes
10. On the Configure the package manager, scan extra installation media, select no, select country for package mirror, stick with default mirror deb.debian.org, blank proxy
11. On Configuring popularity content, select yes or no
12. On Software selection, uncheck Debian desktop environment and Gnome. Check SSH Server and leave standard system utilities checked. Continue.
13. On Configuring grub-pc select yes to install boot loader to primary drive. Select main drive eg. /dev/sda
14. Reboot when complete
15. Remove ISO from virtual CD in Proxmox UI. Select NAS VM, Hardware, CD/DVD Drive, Do not use any media.

##### Pass HDDs to VM

1. Pass shutdown command to NAS VM
2. SSH into Proxmox, and install lshw

`sudo apt install lshw`

3. To determine paths necessary for HDD passthrough.

`lsblk |awk 'NR==1{print $0" DEVICE-ID(S)"}NR>1{dev=$1;printf $0" ";system("find /dev/disk/by-id -lname \"*"dev"\" -printf \" %p\"");print "";}'|grep -v -E 'part|lvm'`

4. Add HDDs to VM via reliable paths ie. /dev/disk/by-id/, 100 is the VM number. Note: Use -scsi1 and -scsi2 for drives.

`sudo qm set 100 -scsix /dev/disk/by-id/wwn-xxxxxxxx`

5. Start NAS VM, login, and make sure that drives are available to VM.

`sudo blkid`

##### Setup RAID 1

1. Install parted and mdadm.

`sudo apt install parted mdadm`

2. Set partition standard for each HDD.

`sudo parted /dev/sdb mklabel gpt`

3. Partition HDD with single ext4.

`sudo parted -a opt /dev/sdb mkpart primary ext4 0% 100%`

4. Create raid array with mdadm

`sudo mdadm --create --verbose /dev/md0 --level=1 --raid-devices=2 /dev/sdb1 /dev/sdc1`

5. Make sure there is an active raid array resyncing

`cat /proc/mdstat`

6. Make sure array is properly formatted

`sudo mkfs.ext4 /dev/md0`

7. Make mountpoint

`sudo mkdir /mnt/raid`

8. Mount array

`sudo mount /dev/md0 /mnt/raid`

9. Configure auto mount with fstab

`echo '/dev/md0 /mnt/raid ext4 defaults,nofail,discard 0 0' | sudo tee -a /etc/fstab`

10. Save raid configuration

`sudo mdadm --detail --scan | sudo tee -a /etc/mdadm/mdadm.conf`

11. Update initramfs

`sudo update-initramfs -u`

##### Install and Setup Samba

1. Install Samba.

`sudo apt install samba -y`

2. Make shared folder on raid array

`sudo mkdir /mnt/raid/storage`

3. Create SMB user and set password

`sudo smbpasswd -a user`

4. Create samba group

`sudo groupadd samba`

5. Add user to group

`sudo usermod -aG samba user`

6. Modify group on shared directory

`sudo chgrp -R samba /mnt/raid/storage`

7. Add group write access to folder

`sudo chmod g+rwx /mnt/raid/storage`

8. Edit samba configuration file

`sudo nano /etc/samba/smb.conf`

9. Append share to bottom of file

```
[Storage]
  comment = Storage Folder
  path = /mnt/raid/storage
  browseable = yes
  read only = no
  guest ok = no
  valid users = @samba
```

10. Restart samba

`sudo systemctl restart samba`

##### Install and Setup Firewall

1. Install UFW

`sudo apt install ufw -y`

2. Allow SSH

`sudo ufw allow ssh`

3. Allow Samba

`sudo ufw allow samba`

4. Enable Firewall

`sudo ufw enable`

5. Show firewall allowed connections - should show 22/tcp and Samba for ipv4 and ipv6
