# Task Warrior
## Backup

```
# cd ~/.task
# tar czf task-backup-$(date +'%Y%m%d').tar.gz *
```

## Taskserver
Taskserver uses port 53589  
Very good: continues access to host server  
Good: not continues access to host server  
New user and new group  
configure the firewall to allow incoming TCP/IP  

## Install
1. [Download](http://taskwarrior.org/download/#dist) Taskwarrior.  
2. Dependencies:
     
    + GnuTLS (ideally version 3.2 or newer)
    + libuuid
    + CMake (2.8 or newer, check with)
    + make
    + A C++ Compiler (GCC 4.7 or Clang 3.0 or newer)
