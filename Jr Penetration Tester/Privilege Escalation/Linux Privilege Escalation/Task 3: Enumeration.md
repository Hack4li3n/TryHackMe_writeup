Be sure to read the task on THM, but here is a summary:
#### Key commands
- System Identification:
   - hostname: Reveals the machine’s name.
   - uname -a: Displays kernel version and system details.
   - /proc/version & /etc/issue: Provide OS version details.
- Process & User Information:
   - ps -A, ps aux: List running processes.
   - id: Shows user privileges and groups.
   - /etc/passwd: Lists system users.
   - history: May reveal past commands, including credentials.
- Privilege Escalation & File Discovery:
   - sudo -l: Checks sudo privileges.
   - ls -la: Lists files with permissions.
   - find: Locates files with specific permissions or modifications.
- Networking:
   - ifconfig: Shows network interfaces.
   - netstat -ano: Displays open connections, listening ports, and network usage.

### Q.1: What is the hostname of the target system?
It is time to start hacking :). 
Start up your AttackBox or if you prefer connect to the target machine by using OpenVPN, using the following command:
```sudo openvpn <file_name>.ovpn```
You then SSH into the target machine by using the provided credentials:
```
Username: karen
Password: Password1
```
We start out easy. To get the hostname of the target system we simply just use ```hostname``` command.

```
$ hostname
wade7363
```
That’s a very easy command to remember isn’t it?
### Answer: wade7363

### Q.2: What is the Linux kernel version of the target system?
There are at least two different ways to find this mentioned in the room. We can either run: ```name -a```
```
$ uname -a
Linux wade7363 3.13.0-24-generic #46-Ubuntu SMP Thu Apr 10 19:11:08 UTC 2014 x86_64 x86_64 x86_64 GNU/Linux
```
But we can also look at ```/proc/version```, which may give us information on the kernel version and additional data such as whether a compiler (e.g. GCC) is installed.
```
$ cat /proc/version
Linux version 3.13.0-24-generic (buildd@panlong) (gcc version 4.8.2 (Ubuntu 4.8.2-19ubuntu1) ) #46-Ubuntu SMP Thu Apr 10 19:11:08 UTC 2014
```
Both commands give the kernel version:
### Answer: 3.13.0–24-generic

### Q.3: What Linux is this?
This one is easy as well. If you read the text you will know that systems can also be identified by looking at the /etc/issue file. This file usually contains some information about the operating system.

Run the following command: 
```
$ cat /etc/issue
Ubuntu 14.04 LTS \n \l
```
There you have the answer (just make sure you ignore the \n \l).
### Answer: Ubuntu 14.04 LTS
### Q.4: What version of the Python language is installed on the system?
I don’t think this is mentioned in the text, but as someone with quite some Python development experience, I know we can simply run:
```
$ python --version
Python 2.7.6
```
Alternatively, you can simply enter the Python interpreter by running python. This will show you the version as well.
### Answer: 2.7.6
### Q.5: What vulnerability seem to affect the kernel of the target system? (Enter a CVE number)
For this we need to do a little bit of googling/searching on Exploit-DB. 3.13.0–24-generic will quickly bring you to a page mentioning CVE-2015–1328, such as:

https://www.rapid7.com/db/modules/exploit/linux/local/overlayfs_priv_esc

That’s all for now. In the near future we will have to exploit this weakness.
### Answer: CVE-2015–1328
