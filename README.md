# Free guides
## Operating System
### Arch Linux
#### Prepare installation Image
* [Download ISO + Verify ISO](doc/en/operating_system/arch_linux/installation/prepare_image.md)  

#### System Installation
[BIOS + MBR + LUKS2 + KEYFILE + LVM](doc/en/operating_system/arch_linux/installation/bios-mbr-luks2-lvm.md)  
+ With */boot* on external device
+ With GRUB as bootloader
+ With BIOS for old mother boards
+ With MBR for old disks
+ With full disk encryption (LUKS2)
+ With keyfile as additional passphrase for fast booting (external device)
+ With LVM on LUKS2
+ With disk cleaning
+ With disk randomize data


[UEFI + GPT + LUKS2 + LVM](doc/en/operating_system/arch_linux/installation/uefi-gpt-luks2-lvm.md)  
+ With */boot* on external device
+ (TODO) With keyfile on external device as additional passphrase for fast booting
+ With GRUB as bootloader
+ With UEFI for new mother boards
+ With GPT for new disks
+ With full disk encryption (LUKS2)
+ With LVM on LUKS2
+ With disk cleaning
+ With disk randomize data


[BIOS + GPT + LUKS2 + LVM](doc/en/operating_system/arch_linux/installation/todo_bios-gpt-luks2-lvm-systemd.md)  
+ With */boot* on external device
+ With GRUB as bootloader
+ With BIOS for old mother boards
+ With GPT for new disks
+ With full disk encryption (LUKS2)
+ With LVM on LUKS2
+ With systemd intramfs
+ With disk cleaning
+ With disk randomize data

## System basic configuration

[System Functionality](doc/en/operating_system/arch_linux/installation/post_installation.md)  
+ Common applications
+ New user


[Keymap Configuration](doc/en/operating_system/arch_install/keymap_configuration.md) Set the keymap layout  

## System audio configuration
## Desktop Environment Configuration
* (TODO) [Wayland](doc/en/desktop_environment/customization/wayland/README.md) is asubstitute for the X server engine and it's the new standard from 2020.
* (TODO) [Weston](doc/en/desktop_environment/customization/weston/README.md) is a classic desktop environment, the default for Wayland engine.
* (TODO) [Sway](doc/en/desktop_environment/customization/sway/README.md) is a tiled window desktop environment similar to i3 but made for Wayland engine.
* (TODO) [i3status bar](doc/en/desktop_environment/customization/i3status/README.md) is a complete fetured and highly customizable status bar.


## Application
### CLI (no GUI)
* [GIT](doc/en/application/git/README.md) is a version control system apllication.  [Taskwarrior](doc/en/application/taskwarrior/installation.md) is a task manager, todo list app
* [GNU Emacs](doc/en/application/emacs/README.md) is a text editor built in ELISP, created by Richard Stallman
* (TODO) [GNUs](doc/en/application/gnus/README.md) is an Email client, News reader, supports RSS.


## Utilities
* [Welcome Message](doc/en/utilities/welcome_message.md) → Display a welcome message at user login.  
* [Multiple Virtual Terminals](doc/en/utilities/multi_virtual_consoles.md) → Create more virtual terminal accessible with *ALT + F1-12*  
* [Copy terminal stdout](doc/en/utilities/clipboard.md) → Using xclip to copy and paste the ouput from comand line.  
