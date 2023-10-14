# Arch-install
*Personal arch installation ( This is not a tutorial, just my newbie way of doing things )*

- Set keyboard apping and checking we're in efi 64
```
loadkeys [keymap]
cat /sys/firmware/efi/fw_platform_size
```
- Connecting to wifi (ethernet should work out of the box)
```
ip link
iwctl
device list
station wlan0 scan
station wlan0 get-networks
station wlan0 connect [SSID]
ping archlinux.org
```
- Partitionnning the disk (disk name here [sda]) with cfdisk, use GPT, create 3 partitions with the specified type :
    - [sda1] : ~1G - EFI  System
    - [sda2] : ~[RAM SIZE]*1,5G - Linux Swap
    - [sda3] : ?G - Linux filesystem
    - Then write it
```
lsblk
cfdisk
```
- Formatting, mounting and activating the created partitions
```
lsblk
mkfs.fat -F32 /dev/[sda1]
mkswap /dev/[sda2]
mkfs.ext4 /dev/[sda3]
mount /dev/sda[sda3] /mnt
mkdir /mnt/boot
mkdir /mnt/boot/efi
mount /dev/[sda1] /mnt/boot/efi
swapon /dev/[sda2]
lsblk
```
- syncing database
```
pacman -Syy
```
- Installing linux core and some necessary utils
```
pacstrap -K /mnt base base-devel linux linux-firmware git pacman-contrib nano
genfstab -U -p /mnt >> /mnt/etc/fstab
arch-chroot /mnt
```
- Setting up region, time, language and hostname ( here [name] ) :
    - delete "#" before your language in /etc/locale.conf, for instance here : "en_US.UTF8"
    - write this in /etc/hosts : "\
    127.0.0.1         localhost\
    ::1               localhost\
    127.0.1.1         [name].localdomain       [name]\
    "
    - Then checking installation with nkinitcpio -P
```
nano /etc/locale.gen
locale-gen
echo “LANG=en_US.UTF-8” > /etc/locale.conf
ln -sf /usr/share/zoneinfo/[Region]/[City] /etc/localtime
hwclock –-systohc
timedatectl set-ntp true
timedatectl
localectl set-keymap [keymap]
echo [name] > /etc/hostname
nano /etc/hosts
mkinitcpio -P
```
- Setting root password, adding user, granting sudo rights
    - delete "#" before "wheel group"
```
passwd
useradd -m -G wheel -s /bin/bash [username]
EDITOR=nano visudo
```
- Seting GRUB
```
pacman -S grub efibootmgr
grub-install --target=x86_64-efi --efi-directory=/boot/efi
grub-mkconfig -o /boot/grub/grub.cfg
```
- Setting NetworkManager and rebooting
```
pacman -S networkmanager
systemctl enable NetworkManager
exit
umount -R /mnt
reboot
```
- Log into root and set [username]'s password, connect to wifi, upgrade system just in case
```
passwd [username]
nmcli device wifi list
nmcli device wifi connect [ssid] password [password]
ping archlinux.org
pacman -Syu
```
- write Colour and ILoveCandy in /etc/pacman.conf UwU
```
pacman -S --needed base-devel
nano /etc/pacman.conf
```
And voilà !

*Sound and backlight :*
- [Backlight](https://wiki.archlinux.org/title/backlight) with Brightnessctl
- [Audio system](https://wiki.archlinux.org/title/sound_system) with pipewire/wireplumber
