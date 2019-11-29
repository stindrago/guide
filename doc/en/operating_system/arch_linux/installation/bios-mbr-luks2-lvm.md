# BIOS + MBR + LUKS2 + LVM
+ With */boot* on external device
+ With GRUB as bootloader
+ With BIOS for old mother boards
+ With MBR for old disks
+ With full disk encryption (LUKS2)
+ With keyfile as additional passphrase for fast booting (external device)
+ With LVM on LUKS2
+ With disk cleaning
+ With disk randomize data

<br>

| **Disk**        | **Partition** | **Size**     | **Partition Type** | **File System** |
| ----------- | --------- | -------- | -------------- | ----------- |
| /dev/sdc    | /dev/sdc1 | 100%FREE | Boot flag      | ext2        |
| /dev/sda    | /dev/sda1 | 100%FREE | Linux  LVM     | -           |

<br><br>

| **Mount Point** | **Partition** | **Device Map Name** | **Physical Volume** | **Volume Group** | **Logical Volume** | **Size**     | **Partition Type** | **File System** |
| ----------- | --------- | --------------- |  -------------- | ------------ | -------------- | -------- | -------------- | ----------- |
| /boot       | /dev/sdc1 | -               | -               | -            | -              | 100%FREE | Boot flag      | ext2        |
| /swap       | /dev/sda1 | cryptlvm        | /dev/sda1       | vg0          | lvswap         | 2G       | Linux LVM      | swap        |
| /           | /dev/sda1 | cryptlvm        | /dev/sda1       | vg0          | lvroot         | 30G      | Linux LVM      | ext4        |
| /tmp        | /dev/sda1 | cryptlvm        | /dev/sda1       | vg0          | lvtmp          | 5G       | Linux LVM      | ext4        |
| /var        | /dev/sda1 | cryptlvm        | /dev/sda1       | vg0          | lvvar          | 20G      | Linux LVM      | ext4        |
| /home       | /dev/sda1 | cryptlvm        | /dev/sda2       | vg0          | lvhome         | 100%FREE | Linux LVM      | xfs         |

<br><br>

```
+----------------+ +---------------------------------------------------------------------------------------------------+
| Boot partition | | Logical volume 1  | Logical volume 2  | Logical volume 3  | Logical volume 4  | Logical volume 5  |
|                | |                   |                   |                   |                   |                   |
| /boot          | | /swap             | /                 | /tmp              | /var              | /home             |
|                | |                   |                   |                   |                   |                   |
|                | | /dev/vg0/lvrswap  | /dev/vg0/lvroot   | /dev/vg0/lvstmp   | /dev/vg0/lvvar    | /dev/vg0/lvhome   |
| (external      | |_ _ _ _ _ _ _ _ _ _|_ _ _ _ _ _ _ _ _ _|_ _ _ _ _ _ _ _ _ _|_ _ _ _ _ _ _ _ _ _|_ _ _ _ _ _ _ _ _ _|
|        device) | |                                                                                                   |
|                | |                                         LUKS2 encrypted partition                                 | 
| /dev/sdc1      | |                                         /dev/sda1                                                 |
+----------------+ +---------------------------------------------------------------------------------------------------+
```

<br>

* `# ls /usr/share/kbd/keymaps/**/*.map.gz` View all keymaps
* `# loadkeys dvorak-programmer` Load keymap, default: *en*
* `# ip a` Show all interfaces
* Connect to Internet
    + Wiered connection
        + `# dhcpcd enp2s0` Get IP address
    + Wireless connection  
        + `# echo 'ctrl_interface=/run/wpa_supplicant' > /etc/wpa_supplicant/wpa_supplicant.conf`
        + `# echo 'update_config=1' >> /etc/wpa_supplicant/wpa_supplicant.conf`
        + `# wpa_supplicant -B -i wlp2s0 -c /etc/wpa_supplicant/wpa_supplicant.conf`
        + `# wpa_cli` Esegui il programma
            + `scan on`
            + `scan_results`
            + `add_network 0`
            + `set_network 0 ssid "wifiName"`
            + `set_network 0 psk "wifiPassword"`
            + `enable_network 0`
            + `save_config`
            + `quit`
        + `# dhcpcd wlp2s0` Get IP address
* `# timedatectl set-ntp true` Update the system clock
* `# fdisk -l` Display partitions
* `# cryptsetup luksFormat --hash=sha512 --key-size=512 --cipher=aes-xts-plain64 --verify-passphrase /dev/sda` Random data generation
* `# cryptsetup luksOpen /dev/sda sda_crypt` Open luks device and map as sda_crypt
* `# dd if=/dev/zero of=/dev/mapper/sda_crypt bs=1M` Clean the sda_crypt (**it will take hours, deppending of the hardisk size**)
* `# cryptsetup luksClose sda_crypt` Destroy the mapping
* `# dd if=/dev/urandom of=/dev/sda bs=512 count=20480` Override the encryption header
* `# dd if=/dev/zero of=/dev/sdc bs=1M` Clean the external device
* `# fdisk /dev/sdc`
    + `o` Create DOS partition table
    + `n` Create new partition /dev/sda1
        + `p` Primary partition type
        + `1` Select partition number 1
        + `default` Default first sector (2048)
        + `default` Default last sector (all disk space)
    + `t` Change partition type /dev/sda1
        + `83` Linux
    + `a` Enable bootable flag on partition 1
    + `w` Write changes to disk
    + `q` Exit fdisk
* `# fdisk /dev/sda`
    + `o` Create DOS partition table
    + `n` Create new partition /dev/sda1
        + `p` Primary partition type
        + `1` Select partition number 1
        + `default` Default first sector (2048)
        + `default` Default last sector (all disk space)
    + `t` Change partition type /dev/sda1
        + `8e` Linux LVM
    + `w` Write changes to disk
    + `q` Exit fdisk
* `# mkdir /root/ramfs` Create directory *ramfs*
* `# mount ramfs /root/ramfs/ -t ramfs` Mount */root/ramfs/*
* `# dd if=/dev/random of=/root/ramfs/keyfile bs=512 count=4 iflag=fullblock` Create keyfile into */root/ramfs/keyfile*
* `# cryptsetup luksFormat --type luks2 /dev/sda1` Create the LUKS2 encrypted container
* `# cryptsetup luksAddKey /dev/sda1 /root/ramfs/keyfile` Add a new passphrase stored into */root/ramfs/keyfile*
* `# cryptsetup open /dev/sda1 cryptlvm` Open the container
* `# lvmdiskscan` List possible phisical volumes
* `# modprobe dm_crypt` Load kernel moules for encryption
* `# modprobe dm_mod` Load kernel moules for lvm
* `# pvcreate /dev/mapper/cryptlvm` Create physical volume
* `# vgcreate vg0 /dev/mapper/cryptlvm` Create volume group *vg0* from physical volume *cryptlvm*
* `# free -h` Display RAM size
* `# lvcreate -C y -L 2G vg0 -n lvswap` Create logical volume *lvswap* (2x RAM size)
* `# lvcreate -L 30G vg0 -n lvroot` Create logical volume *lvroot*
* `# lvcreate -L 5G vg0 -n lvtmp` Create logical volume *lvtmp*
* `# lvcreate -L 20G vg0 -n lvvar` Create logical volume *lvvar*
* `# lvcreate -l 100%FREE vg0 -n lvhome` Create logical volume *lvhome* (remaning space)
* `# vgscan`
* `# vgchange -ay`
* `# mkfs.ext2 /dev/sdc1` Format filesystem *ext2*
* `# mkswap /dev/vg0/lvswap` Format filesystem *swap*
* `# mkfs.ext4 /dev/vg0/lvroot` Format filesystem *ext4*
* `# mkfs.ext4 /dev/vg0/lvtmp` Format filesystem *ext4*
* `# mkfs.ext4 /dev/vg0/lvvar` Format filesystem *ext4*
* `# mkfs.xfs /dev/vg0/lvhome` Format filesystem *xfs*
* `# mount /dev/vg0/lvroot /mnt` Mount *lvroot*
* `# mkdir /mnt/hostlvm` Create directory */hostlvm*
* `# mkdir /mnt/boot` Create directory */boot*
* `# mkdir /mnt/tmp` Create directory */tmp*
* `# mkdir /mnt/var` Create directory */var*
* `# mkdir /mnt/home` Create directory */home*
* `# mount --bind /run/lvm /mnt/hostlvm` Mount bind directory */run/lvm*
* `# mount /dev/sdc1 /mnt/boot` Mount directory */boot*
* `# mkdir /mnt/boot/keys` Create directory */boot/keys*
* `# mount /dev/vg0/lvtmp /mnt/tmp` Mount *lvtmp*
* `# mount /dev/vg0/lvvar /mnt/var` Mount *lvvar*
* `# mount /dev/vg0/lvhome /mnt/home` Mount *lvhome* 
* `# swapon /dev/vg0/lvswap` Mount *lvswap*
* `# cp -p /root/ramfs/keyfile /mnt/boot/keys/secretkey` Copy keyfile into external boot device
* `# chmod 600 /mnt/boot/keys/secretkey` Deny user access
* `# umount /root/ramfs` Umount */root/ramfs*
* `# pacstrap -i /mnt base base-devel` Install base system
    + `default`
    + `default`
    + `default`
* `# genfstab -U /mnt >> /mnt/etc/fstab`
* `# arch-root /mnt`
* `# ln -s /hostlvm /run/lvm` Create sys link to */run/lvm*
* `# pacman -S grub linux-headers wpa_supplicant wireless_tools` Install additional packages
* `# sed 's/^#it_IT.UTF-8 UTF-8/it_IT.UTF-8 UTF-8/' /etc/locale.gen > /tmp/locale.gen` Uncomment *it_IT.UTF-8 UTF-8* in */etc/locale.gen*
* `# cp /tmp/locale.gen /etc/locale.gen`
* `# locale-gen` Generate locales
* `# echo 'LANG=it_IT.UTF-8' > /etc/locale.conf` Set the *LANG* variable
* `# echo 'KEYMAP=dvorak-programmer' > /etc/vconsole.conf` If you set the keyboard layout, default: *KEYMAP=en*
* `# ln -sf /usr/share/zoneinfo/Europe/Rome` Set time zone
* `# hwclock --systohc --utc` Sync clock
* `# echo 'csd_arch' > /etc/hostname` Set hostame (The nickname of your machine)
* `# echo '127.0.0.1	localhost' >> /etc/hosts` Set local host part 1
* `# echo '::1        localhost' >> /etc/hosts` Set local host part 2
* `# echo '127.0.1.1	csd_arch.localdomain	csd_arch' >> /etc/hosts` Set local host part 3
* `# passwd` Add root password
* `# sed 's/^MODULES=.*/MODULES=(ext2)/' /etc/mkinitcpio.conf > /etc/mkinitcpio.conf` Add *ext2* modules to */etc/mkinitcpio.conf*
* `# sed 's/^HOOKS=.*/HOOKS=(base udev autodetect keyboard keymap modconf block encrypt lvm2 filesystems fsck)/' /etc/mkinitcpio.conf > /tmp/mkinitcpio.conf` Add the *keyboard*, *encrypt* and *lvm2* hooks to */etc/mkinitcpio.conf*
* `# cp /tmp/mkinitcpio.conf /etc/mkinitcpio.conf`
* `# mkinitcpio -p linux` Create a new initramfs
* `# sed 's#^GRUB_CMDLINE_LINUX_DEFAULT=.*#GRUB_CMDLINE_LINUX_DEFAULT="cryptdevice=/dev/sda1:vg0  cryptkey=/dev/sdb1:ext2:/keys/secretkey quiet"#' /etc/default/grub > /tmp/grub` Edit GRUB config */etc/default/grub*
* `# cp /tmp/grub /etc/default/grub`
* `# grub-install --target=i386-pc --recheck --debug /dev/sda` Install grub
* `# cp /usr/share/locale/it/LC_MESSAGES/grub.mo /boot/grub/locale/it.mo`
* `# grub-mkconfig -o /boot/grub/grub.cfg` Make config files
* `# exit` Exit *arch-chroot*
* `# umount -R /mnt` Umount all the partitions
* `# reboot` Restart the system
