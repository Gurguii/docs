# [UEFI] Arch Linux installation example - wired

#### GitHub: [https://github.com/Gurguii](https://github.com/Gurguii)  

Since I've done this quite a few times now, I thought it would be cool to make a simple example  
that can be used for reference as a guide or to come and check certain steps looking for commands :P  
  
## Starting Point

#### Disk Information

Disk | Size  
---- | ----
`/dev/sda` | 100G

#### Partition Scheme

Disk Partition | Mountpoints | Size
------------ | ------------ | -------------
/dev/sda1| `/boot/efi` | 500M
/dev/sda2| `[swap]` | 8G
/dev/sda3| `/` | 36G
/dev/sda4| `/home` | 55.5G

# Installation

## 1. Disk Partitioning

**Note**: fdisk is not the only tool for this purpose. You can use `parted`, `gdisk`, `gpart`, `cfdisk`, `cgdisk`...

### Boot partition - efi

```bash
echo -e "g\nn\n1\n\n+500M\nw\n" | fdisk /dev/sda
```

### Swap partition - swap

```bash
echo -e "n\n2\n\n+8G\nt\n2\nswap\nw\n" | fdisk /dev/sda
```

### Root partition - linux filesystem  

```bash
echo -e "n\n3\n\n+36G\nt\n3\nlinux\nw\n" | fdisk /dev/sda
```

### Home partition - linux filesystem

```bash
echo -e "n\n4\n\n\nwt\n4\nlinux\nw" | fdisk /dev/sda
```

### Make kernel aware of changes

```bash
partprobe
```

## 2. Filesystem Creation  

command | usage  
---- | ----
mkfs.vfat -F 32 /dev/sda1 | uefi  
mkswap /dev/sda2 | swap
mkfs.ext4 /dev/sda3 | linux
mkfs.ext4 /dev/sda4 | linux  

## 3. Partition Mounting

```bash
mount /dev/sda3 /mnt
mkdir -p /mnt/home
mkdir -p /mnt/boot/efi
mount /dev/sda4 /mnt/home
mount /dev/sda1 /mnt/boot/efi
swapon /dev/sda2
```

## 4. Base System Installation

```bash
pacstrap -K /mnt base linux linux-lts linux-headers linux-firmware networkmanager base-devel sof-firmware net-tools sudo
```  

***Note:*** *You can go minimal and install 'linux base networkmanager'.*

## 5. New System Configuration

#### Get into our new system

`arch-chroot /mnt`  

#### Create our sudo user

`useradd -m <username>`

#### Make members of the group wheel able to execute ANY command as sudo

`sed -i 's/^# %wheel ALL=(ALL:ALL) ALL$/%wheel ALL=(ALL:ALL) ALL/' /etc/sudoers`

#### Add the new user to the wheel group

`usermod -aG wheel <username>`

#### Enable NetworkManager

`systemctl enable NetworkManager`

#### Set the hostname

`echo '<hostname>' > /etc/hostname`

#### Set keyboard layout

`echo 'KEYMAP=<lang>' > /etc/vconsole.conf`

## 6. Install Bootloader  

In this case, I'll be using grub  

#### Install grub package and efibootmgr

`pacman -S --noconfirm grub efibootmgr`  

#### Install grub bootloader with custom bootloader id (name that will appear in the boot menu)

`grub-install --target=x86_64-efi --efi-directory=/boot/efi/ --bootloader-id=<desired_menu_name>`  

#### Update grub config

`grub-mkconfig -o /boot/grub/grub.cfg`

## 7. Last few steps

#### Exit the chroot environment

`exit`  

#### Generate new /etc/fstab file to mount partitions upon boot

`genfstab /mnt > /mnt/etc/fstab`  

#### Umount every partition and disable the swap partition

`umount -a && swapoff /dev/sda2`  

#### Reboot the system  

`reboot`

## 8. Enjoy the new system

If everything works as expected, our system should now boot within grub menu with our new system as an entry of the menu, the name will be the value given in the `--bootloader-id` param.  

At this point you can go headless or install a graphical environment.

## Optional Steps

### [ Install Graphical Environment ]

***Note:*** *Requires elevated privileges.*  
| Graphical Environment | Command to Install          | Display Manager             | Command to Install DM       | Characteristics                           |
|-----------------------|-----------------------------|-----------------------------|----------------------------|-------------------------------------------|
| GNOME                 | `sudo pacman -S gnome`      | GDM (GNOME Display Manager)| `sudo pacman -S gdm`        | Modern, feature-rich desktop environment. |
| KDE Plasma            | `sudo pacman -S plasma`     | SDDM (Simple Desktop Display Manager)| `sudo pacman -S sddm` | Highly customizable, visually appealing. |
| Xfce                  | `sudo pacman -S xfce4`      | LightDM                     | `sudo pacman -S lightdm`   | Lightweight, fast, user-friendly.         |
| LXQt                  | `sudo pacman -S lxqt`       | LightDM                     | `sudo pacman -S lightdm`   | Lightweight, Qt-based desktop environment.|
| Cinnamon              | `sudo pacman -S cinnamon`   | LightDM                     | `sudo pacman -S lightdm`   | User-friendly, Windows-like interface.   |
| MATE                  | `sudo pacman -S mate`       | LightDM                     | `sudo pacman -S lightdm`   | Traditional, easy-to-use desktop environment. |
  
  Choose the one you please and install both the graphical environment and display manager.  
  Then enable the display manager service:  
`sudo systemctl enable <gdm|sddm|lightdm>`

### [ Set static IP address ]

Follow this just if you have NetworkManager as your network manager and change values to fit your needs / desires.  

### Create connection profile with nmcli

```bash
nmcli conn add con-name static \  
ifname eth0 \  
ipv4.addresses 192.168.1.10/24 \  
ipv4.dns 8.8.8.8,8.8.4.4 \  
ipv4.method manual \  
ipv4.connection.autostart 1
```

### Set our new static ip profile  

#### Disable default dhcp profile

`nmcli conn down 'Wired connection 1'`

#### Enable our static profile  

`nmcli conn up static`  

#### Restart NetworkManager service to avoid any issue with profiles

`sudo systemctl restart NetworkManager`

### Check that everything is OK  

#### Check if we got our new ip  

`ip a`

#### Lil ping to check internet access  

`ping google.com`
