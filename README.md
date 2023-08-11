# Arch-install
Personal arch installation

loadkeys fr
cat /sys/firmware/efi/fw_platform_size
ip link
iwctl
device list
station wlan0 scan
station wlan0 get-networks
station wlan0 connect [SSID]
ping archlinux.org
pacman -Syy
lsblk
cfdisk
\> Gpt
[sda1] : 1G - EFI  System
[sda2] : RAM*1,5G - Linux Swap
[sda3] : ?G - Linux filesystem
\> Write
lsblk
mkfs.fat -F32 /dev/[sda1]
chmod 0600 /dev/[sda2]
mkswap /dev/[sda2]
mkfs.ext4 /dev/[sda3]
mount /dev/sda[sda3] /mnt
mkdir /mnt/boot
mkdir /mnt/boot/efi
mount /dev/[sda1] /mnt/boot/efi
swapon /dev/[sda2]
lsblk
reflector -–verbose -l 100 -n 20 -c fr -p https --sort rate --save /etc/pacman.d/mirrorlist
pacman -Syy
pacstrap -K /mnt base base-devel linux linux-firmware #revoir nano
genfstab -U -p /mnt >> /mnt/etc/fstab
arch-chroot /mnt
pacman -Syy
nano /etc/locale.gen
\> -# en_US.UTF8
locale-gen
echo “LANG=en_US.UTF-8” > /etc/locale.conf
ln -sf /usr/share/zoneinfo/Europe/Paris /etc/localtime
hwclock –-systohc
timedatectl set-ntp true # revoir
timedatectl
echo [name] > /etc/hostname
nano /etc/hosts
\>"127.0.0.1         localhost
::1               localhost
127.0.1.1         [name].localdomain       [name]"
mkinitcpio -P
passwd
\> root passwd
useradd -m -G wheel -s /bin/bash nico
EDITOR=nano visudo
\> -# wheel group
pacman -S grub efibootmgr
grub-install --target=x86_64-efi --efi-directory=/boot/efi
grub-mkconfig -o /boot/grub/grub.cfg
localectl set-keymap fr #revoir
pacman -S networkmanager
systemctl enable NetworkManager
exit
umount -R /mnt
reboot
/> Log in root
passwd nico
nmcli device wifi list
nmcli device wifi connect [ssid] password [password]
ping archlinux.org
pacman -Syu
pacman -S --needed base-devel
nano /etc/pacman.conf
\> -# Colour + ILoveCandy
git clone htyps://aur.archlinux.org/paru.git
cd paru
makepkg -si
rm -r paru