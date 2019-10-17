# Welcome Message
How to display a welcome message when you login into a virtual console.  

## Global
`# emacs /etc/motd` *motd* stands for message of the day. The file is a text file not a script.  
`# emacs /etc/profile.d/<filename>.sh` *profile.d* is a directory included by */etc/profile* at login. Create your own script and there. The script will execute after the login.  

## User
`# emacs ~/.bashrc` Edit the file. If you add *echo "hello"* it will display a hello message when the user will login into a new virtual console.  

## Usage
For example if multiple users have access to a server it is possible to create a info message specific for them.  
It can be usefull to inform the admins for issues, or just saying hello.  
I use the welcome message with [task-warrior](https://taskwarrior.org/) to display the daily tasks.  