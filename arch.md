
# Arch Linux Installation Guide

### 1. Connect to the Internet using iwctl
First, start the service:
```bash
sudo systemctl start iwd
```

### 2. Set the time
```bash
timedatectl
```

### 3. Check disk information
```bash
fdisk -l
```
(Check the SSD name.)

### 4. Partition the disk
```bash
fdisk /dev/diskname
```
Make the partitions with:

- Create GPT label: `g`
- Create EFI system partition, swap, root, and home.

### 5. Format the partitions
- Root: 
  ```bash
  mkfs.ext4 /dev/rootpartition
  ```
- Home: 
  ```bash
  mkfs.ext4 /dev/homepartition
  ```
- Swap: 
  ```bash
  mkswap /dev/swappartition
  ```
- EFI: 
  ```bash
  mkfs.fat -F 32 /dev/efipartition
  ```

### 6. Mount the partitions
- Mount root: 
  ```bash
  mount /dev/rootpartition /mnt
  ```
- Mount home: 
  ```bash
  mount --mkdir /dev/homepartition /mnt/home
  ```
- Mount EFI: 
  ```bash
  mount --mkdir /dev/efipartition /mnt/boot/efi
  ```
- Enable swap: 
  ```bash
  swapon /dev/swappartition
  ```

### 7. Install the base system
```bash
pacstrap -K /mnt base linux linux-firmware sof-firmware nano grub efibootmgr base-devel networkmanager
```

### 8. Generate fstab
```bash
genfstab /mnt > /mnt/etc/fstab
```

### 9. Enter the installed system
```bash
arch-chroot /mnt
```

### 10. Set time
- Link timezone:
  ```bash
  ln -sf /usr/share/zoneinfo/Asia/Kolkata /etc/localtime
  ```
- Sync time:
  ```bash
  hwclock --systohc
  ```

### 11. Set localization
- Open locale file:
  ```bash
  nano /etc/locale.gen
  ```
- Uncomment `en_US.UTF-8 UTF-8`.
- Generate locales:
  ```bash
  locale-gen
  ```

### 12. Create locale.conf
- Open file:
  ```bash
  nano /etc/locale.conf
  ```
- Add:
  ```bash
  LANG=en_US.UTF-8
  ```

### 13. Set hostname
- Open file:
  ```bash
  nano /etc/hostname
  ```
- Add your hostname.

### 14. Set root password
```bash
passwd
```

### 15. Add user
- Add user:
  ```bash
  useradd -m -G wheel -s /bin/bash username
  ```
- Set user password:
  ```bash
  passwd username
  ```

### 16. Enable sudo for user
- Open sudoers:
  ```bash
  EDITOR=nano visudo
  ```
- Uncomment:
  ```bash
  %wheel ALL=(ALL) ALL
  ```

### 17. Enable NetworkManager
```bash
systemctl enable NetworkManager
```

### 18. Install and configure GRUB
- Install GRUB:
  ```bash
  grub-install /dev/sda
  ```
- Configure GRUB:
  ```bash
  grub-mkconfig -o /boot/grub/grub.cfg
  ```

### 19. Finalize installation
- Exit chroot:
  ```bash
  exit
  ```
- Unmount partitions:
  ```bash
  umount -a
  ```
- Reboot:
  ```bash
  reboot
  ```

### 20. Install desktop environment
After reboot, install essential packages:
```bash
sudo pacman -S budgie ly papirus-icon-theme arc-gtk-theme alacritty
```
