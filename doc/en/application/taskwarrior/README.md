## Install
### Manual

1. [Download Taskwarrior](http://taskwarrior.org/download/#dist)
2. Dependencies:
	+ GnuTLS (ideally version 3.2 or newer)
	+ libuuid
	+ CMake (2.8 or newer, check with)
	+ make
	+ A C++ Compiler (GCC 4.7 or Clang 3.0 or newer)


## Configuration
###Backup

Create a compressed file
```
# cd ~/.task
# tar czf task-backup-$(date +'%Y%m%d').tar.gz *
```


## Taskserver
### Info

Taskserver uses port 53589  
Very good: continues access to host server  
Good: not continues access to host server  
New user and new group  
configure the firewall to allow incoming TCP/IP  

### Scripts
[Service](taskd.service)  
[Taskserver Setup](taskserver_setup.sh)  
