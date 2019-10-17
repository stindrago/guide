# Multiple Vilutal Consoles
## Install
`# pacman -S  util-linux` The pkg is *getty* or *agetty* for Arch.  

## Configuration
`# emacs /etc/systemd/logind.conf` Edit file and set the option *NAutoVTs=6* to the number of virtual terminals that you want at boot  

# Usage
`# systemctl start getty@ttyN.service` Start a temporary new virtual console. *N* stands for a character.  

# Examples
`# systemctl start getty@tty7.service` Start a temporary new virtual console on tty7 accessible by pressing *ALT + F7*  

# More
For more keyboard shorcuts to open more virtual conoles go [here](r/doc/en/operating_system/arch_install/keymap_configuration.md)  
More info for getty [here](https://wiki.archlinux.org/index.php/Getty)  