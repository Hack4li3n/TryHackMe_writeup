Searching the target system for important information and potential privilege escalation vectors can be fruitful. The built-in ```find``` command is useful and worth keeping in your arsenal.

Below are some useful examples for the ```find``` command. 

#### Find files: 
- ```find . -name flag1.txt```: find the file named “flag1.txt” in the current directory
- ```find /home -name flag1.txt```: find the file names “flag1.txt” in the /home directory
- ```find / -type d -name config```: find the directory named config under “/”
- ```find / -type f -perm 0777```: find files with the 777 permissions (files readable, writable, and executable by all users)
- ```find / -perm a=x```: find executable files
- ```find /home -user frank```: find all files for user **“frank”** under **“/home”**
- ```find / -mtime 10```: find files that were modified in the last 10 days
- ```find / -atime 10```: find files that were accessed in the last 10 day
- ```find / -cmin -60```: find files changed within the last hour (60 minutes)
- ```find / -amin -60```: find files accesses within the last hour (60 minutes)
- ```find / -size 50M```: find files with a 50 MB size

This command can also be used with (+) and (-) signs to specify a file that is larger or smaller than the given size. 

Folders and files that can be written to or executed from: 
- ```find / -writable -type d 2>/dev/null```: Find world-writeable folders
- ```find / -perm -222 -type d 2>/dev/null```: Find world-writeable folders
- ```find / -perm -o w -type d 2>/dev/null```: Find world-writeable folders
- ```find / -perm -o x -type d 2>/dev/null```: Find world-executable folders

Find development tools and supported languages: 
- ```find / -name perl*```
- ```find / -name python*```
- ```find / -name gcc*```

Find specific file permissions: 

Below is a short example used to find files that have the SUID bit set. The SUID bit allows the file to run with the privilege level of the account that owns it, rather than the account which runs it. This allows for an interesting privilege escalation path,we will see in more details on task 6. The example below is given to complete the subject on the “find” command. 
- ```find / -perm -u=s -type f 2>/dev/null```: Find files with the SUID bit, which allows us to run the file with a higher privilege level than the current user. 
