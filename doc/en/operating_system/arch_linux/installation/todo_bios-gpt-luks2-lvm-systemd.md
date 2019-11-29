# BIOS + GPT + LUKS2 + LVM
+ With */boot* on external device
+ With GRUB as bootloader
+ With BIOS for old mother boards
+ With GPT for new disks
+ With full disk encryption (LUKS2)
+ With LVM on LUKS2
+ With systemd intramfs
+ With disk cleaning
+ With disk randomize data

<br>

| **Disk**	     | **Info**                      |
| -------------- | ----------------------------- |
| /dev/sdb       | External device for bios grub |
| /dev/sda1      | Disk for BIOS boot            |
| /dev/sda2      | Disk for LVM root             |

<br><br>

| **Mount Point** | **Path**        | **Label**       | **Device Map Name** | **Physical Volume** | **Volume Group** | **Logical Volume** | **Size**     | **Partition Type** | **File System** |
| --------------- | --------------- | --------------- | ------------------- |  ------------------ | ---------------- | ------------------ | ------------ | ------------------ | --------------- |
| /boot           | /dev/sdb1       | BOOT            | -                   | -                   | -                | -                  | 400M         | Linux              | ext2            |
| -               | /dev/sda1       |  -              | -                   | -                   | -                | -                  | 1M           | BIOS boot          | -               |
| /swap           | /dev/vg0/lvswap | SWAP            | lvmcrypt            | /dev/sda2           | vg0              | lvswap             | 32G          | Linux LVM          | swap            |
| /               | /dev/vg0/lvroot | ROOT            | lvmcrypt            | /dev/sda2           | vg0              | lvroot             | 30G          | Linux LVM          | ext4            |
| /tmp            | /dev/vg0/lvtmp  | TMP             | lvmcrypt            | /dev/sda2           | vg0              | lvtmp              | 5G           | Linux LVM          | ext4            |
| /var            | /dev/vg0/lvvar  | VAR             | lvmcrypt            | /dev/sda2           | vg0              | lvvar              | 20G          | Linux LVM          | ext4            |
| /home           | /dev/vg0/lvhome | HOME            | lvmcrypt            | /dev/sda2           | vg0              | lvhome             | 100%FREE     | Linux LVM          | xfs             |

<br><br>

```
+----------------++-----------------------------------------------------------------------------------------------------------------------+
| Boot partition || BOOT Bios         | Logical volume 1  | Logical volume 2  | Logical volume 3  | Logical volume 4  | Logical volume 5  |
|                ||     parition      |                   |                   |                   |                   |                   |
| /boot          ||                   | /swap             | /                 | /tmp              | /var              | /home             |
|                ||                   |                   |                   |                   |                   |                   |
|                ||                   | /dev/vg0/lvrswap  | /dev/vg0/lvroot   | /dev/vg0/lvstmp   | /dev/vg0/lvvar    | /dev/vg0/lvhome   |
| (external      ||                   |_ _ _ _ _ _ _ _ _ _|_ _ _ _ _ _ _ _ _ _|_ _ _ _ _ _ _ _ _ _|_ _ _ _ _ _ _ _ _ _|_ _ _ _ _ _ _ _ _ _|
|        device) ||                   |                                                                                                   |
|                ||                   |                                         LUKS2 encrypted partition                                 |
| /dev/sdb1      || /dev/sda1         |                                         /dev/sda2                                                 |
+----------------++-----------------------------------------------------------------------------------------------------------------------+
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
        + `# wpa_cli -i wlp2s0` Esegui il programma
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
* Set config variables for common used paths
    + `# VAR_OS="/dev/sda"` Variable where OS is going to be installed (nvme0n1 for SSD)
    + `# VAR_BOOT="/dev/sdc"` Variable where the GRUB is going to be installed (On */dev/sdb* is the installation OS process)
* `# dd if=/dev/zero of=$VAR_BOOT bs=1M &> /dev/null &` Clean the external *boot* device in background
* `# cryptsetup luksFormat --hash=sha512 --key-size=512 --cipher=aes-xts-plain64 --verify-passphrase $VAROS` Generate randomn data on the device
* `# cryptsetup luksOpen $VAR_OS tmpcrypt` Open luks device and map as *tmpcrypt*
* `# dd if=/dev/zero of=/dev/mapper/tmpcrypt bs=1M status=progress` Clean tmpcrypt (**it will take hours, deppending of the hardisk size**)
* `# cryptsetup luksClose tmpcrypt` Destroy the mapping
* `# dd if=/dev/urandom of=$VAR_OS bs=512 count=20480` Override the *tmpcrypt* encryption header
* `# fdisk $VAR_OS`
    + `g` Create GPT partition table
    + `n` Create new partition
        + `default` Primary partition
        + `default` Default starting sector (2048)
        + `+1M` Ending sector (1 Megabytes)
    + `t` Change partition type
        + `4` BIOS boot
    + `n` Create new partition
        + `default` Default second partition (2)
        + `default` Default starting sector (4096)
        + `default` Default ending sector (remaining disk space)
    + `t` Change partition type
        + `2` Select second partition
        + `31` Linux LVM
    + `w` Write changes to disk
* `# fdisk $VAR_BOOT`
    + `g` Create GPT partition table
    + `n` Create new partition
        + `default` Default first partition (1)
        + `default` Default starting sector (2048)
        + `default` Default ending sector (remaining disk space)
    + `w` Write changes to disk
* `# cryptsetup luksFormat --type luks2 $VAR_OS` Create the LUKS2 encrypted container
* `# cryptsetup luksOpen $VAR_OS lvmcrypt` Open the container
* `# lvmdiskscan` List possible phisical volumes
* `# modprobe dm_crypt` Load kernel moules for encryption
* `# modprobe dm_mod` Load kernel moules for lvm
* `# pvcreate --dataalignment 1m /dev/mapper/lvmcrypt` Create physical volume (*dataalignament 1m* is only for ssd)
* `# vgcreate vg0 /dev/mapper/lvmcrypt` Create volume group *vg0* from physical volume *cryptlvm*
* `# free -h` Display RAM size
* `# lvcreate -C y -L 2G vg0 -n lvswap` Create logical volume *lvswap* (2x RAM size)
* `# lvcreate -L 30G vg0 -n lvroot` Create logical volume *lvroot*
* `# lvcreate -L 5G vg0 -n lvtmp` Create logical volume *lvtmp*
* `# lvcreate -L 20G vg0 -n lvvar` Create logical volume *lvvar*
* `# lvcreate -l 100%FREE vg0 -n lvhome` Create logical volume *lvhome* (remaning space)
* `# vgscan`
* `# vgchange -ay`
* `# mkfs.ext2 ${VAR_BOOT}1 -L BOOT` Format the boot partition to *ext2* filesystem
* `# mkswap /dev/vg0/lvswap -L SWAP` Format filesystem *swap*
* `# mkfs.ext4 /dev/vg0/lvroot -L ROOT` Format filesystem *ext4*
* `# mkfs.ext4 /dev/vg0/lvtmp -L TMP` Format filesystem *ext4*
* `# mkfs.ext4 /dev/vg0/lvvar -L VAR` Format filesystem *ext4*
* `# mkfs.xfs /dev/vg0/lvhome -L HOME` Format filesystem *xfs*
* `# mount /dev/vg0/lvroot /mnt` Mount *lvroot* before anything else
* `# mkdir /mnt/hostlvm` Create directory */hostlvm*
* `# mkdir /mnt/boot` Create directory */boot*
* `# mkdir /mnt/tmp` Create directory */tmp*
* `# mkdir /mnt/var` Create directory */var*
* `# mkdir /mnt/home` Create directory */home*
* `# mount --bind /run/lvm /mnt/hostlvm` Mount bind directory */run/lvm*
* `# mount ${VAR_BOOT}1 /mnt/boot` Mount directory */boot*
* `# mount /dev/vg0/lvtmp /mnt/tmp` Mount *lvtmp*
* `# mount /dev/vg0/lvvar /mnt/var` Mount *lvvar*
* `# mount /dev/vg0/lvhome /mnt/home` Mount *lvhome* 
* `# swapon /dev/vg0/lvswap` Mount *lvswap*
* `# pacstrap /mnt base base-devel` Install base system
* `# genfstab -U /mnt >> /mnt/etc/fstab`
* `# arch-root /mnt`
* `# ln -sf /hostlvm /run/lvm` Create sys link to */run/lvm*
* `# pacman -S grub linux-headers wpa_supplicant wireless_tools emacs-nox git` Install additional packages
* `# sed 's/^#en_US.UTF-8 UTF-8/es_US.UTF-8 UTF-8/' /etc/locale.gen > /tmp/locale.gen` Uncomment *en_US.UTF-8 UTF-8* in */etc/locale.gen*
* `# cp /tmp/locale.gen /etc/locale.gen`
* `# locale-gen` Generate locales
* `# echo 'LANG=en_US.UTF-8' > /etc/locale.conf` Set the *LANG* variable
* `# echo 'KEYMAP=dvorak-programmer' > /etc/vconsole.conf` If you set the keyboard layout, default: *KEYMAP=en*
* `# ln -sf /usr/share/zoneinfo/Europe/Rome /etc/localtime` Set time zone
* `# hwclock --systohc --utc` Sync clock
* `# echo 'hostname' > /etc/hostname` Set hostame (The nickname of your machine)
* `# echo '127.0.0.1	localhost' >> /etc/hosts` Set local host part 1
* `# echo '::1        localhost' >> /etc/hosts` Set local host part 2
* `# echo '127.0.1.1	hostname.localdomain	hostname' >> /etc/hosts` Set local host part 3
* `# passwd` Add root password
* `# sed 's/^MODULES=.*/MODULES=(ext2)/' /etc/mkinitcpio.conf > /tmp/mkinitcpio.conf.tmp` Add *ext2* modules to */etc/mkinitcpio.conf*
* `# sed 's/^HOOKS=.*/HOOKS=(base systemd autodetect keyboard sd-vconsole modconf block sd-encrypt sd-lvm2 filesystems fsck)/' /tmp/mkinitcpio.conf.tmp > /tmp/mkinitcpio.conf` Add the *keyboard*, *encrypt* and *lvm2* hooks to */etc/mkinitcpio.conf*
* `# cp /tmp/mkinitcpio.conf /etc/mkinitcpio.conf`
* `# mkinitcpio -p linux` Create a new initramfs
* `# blkid` Check all UUID or PARTUUID
* Set config variables for common used paths
    + `# VAR_OS="/dev/sda2"` Variable to disk
    + `# VAR_ROOT="/dev/vg0/lvroot"` Variable to lvroot
    + `# VAR_SWAP="/dev/vg0/lvswap"` Variable to lvswap
* (NOTWORKING) `# VAR_OS_UUID=$(lsblk ${VAR_OS} -no UUID)` Variable with the dusk UUID
* `# VAR_SWAP_UUID=$(lsblk ${VAR_SWAP} -no UUID)` Variable with the lvswap UUID
* `# VAR_KEY_UUID=$(lsblk ${VAR_KEY}1 -no UUID)` Variable with the keyfile device UUID
* `# sed 's#^GRUB_CMDLINE_LINUX_DEFAULT=.*#GRUB_CMDLINE_LINUX_DEFAULT="rd.luks.name='${VAR_OS_UUID}':lvmcrypt root='${VAR_ROOT}' resume='${VAR_SWAP}' quiet"#' /etc/default/grub > /tmp/grub` Edit GRUB config */etc/default/grub*
* `# cp /tmp/grub /etc/default/grub`
* `# grub-install --efi-directory=/efi --bootloader-id=UEFI_GRUB --recheck` Install grub
* `# cp /usr/share/locale/en\@quot/LC_MESSAGES/grub.mo /boot/grub/locale`
* `# grub-mkconfig -o /boot/grub/grub.cfg` Make config files
* `# exit` Exit *arch-chroot*
* `# umount -R /mnt` Umount all the partitions
* `# reboot` Restart the system
