# Try Hack Me: Windows Privilege Escalation Complete Write-up
I'm writing my own write-up on the lab named [Windows Privilege Escalation](https://tryhackme.com/room/windowsprivesc20) from [tryhackme](https://tryhackme.com) which is pretty tough to solve and fun to learn. Let's start! 

![png](https://cyb3r53c.com/wp-content/uploads/2023/03/PrivilegeEscalation.png)

**I will be skipping over the following tasks since it is read-only to complete:**
- Task 1: Introduction
- Task 8: Tools of the Trade
- Task 9: Conclusion

## Task 1: Introduction
During a penetration test, you will often have access to some Windows hosts with an unprivileged user. Unprivileged users will hold limited access, including their files and folders only, and have no means to perform administrative tasks on the host, preventing you from having complete control over your target.

This room covers fundamental techniques that attackers can use to elevate privileges in a Windows environment, allowing you to use any initial unprivileged foothold on a host to escalate to an administrator account, where possible.

If you want to brush up on your skills first, you can have a look through the [Windows Fundamentals Module](https://tryhackme.com/module/windows-fundamentals) or the [Hacking Windows Module](https://tryhackme.com/module/hacking-windows-1).

### Q: Click and continue learning!
#### Answer: No answer needed

## Task 2: Windows Privilege Escalation
Privilege escalation involves leveraging existing access to a system to gain higher privileges, often aiming for administrative control. This can be done by exploiting weaknesses such as misconfigurations, excessive privileges, vulnerable software, or missing security patches.

Windows users are categorized as:
- Administrators: Have full system control.
- Standard Users: Have limited access and cannot make major changes.

Additionally, special built-in accounts exist:
- SYSTEM: Higher privileges than administrators.
- Local Service: Runs services with minimal privileges.
- Network Service: Runs services using network authentication.

### Q.1: Users that can change system configurations are part of which group?
#### Answer: Administrators
### Q.2: The SYSTEM account has more privileges than the Administrator user (aye/nay)
#### Answer: aye

## Task 3: Harvesting Passwords from Usual Spots
1. Unattended Windows Installations
  Administrators often leave credentials in configuration files used for automated installations:
  - C:\Unattend.xml
  - C:\Windows\Panther\Unattend.xml
  - C:\Windows\system32\sysprep.inf
2. PowerShell History
  PowerShell logs executed commands, including those containing passwords. Retrieve them via:
  ```type %userprofile%\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadline\ConsoleHost_history.txt```
3. Saved Windows Credentials
   Windows allows users to store credentials, which can be listed with: ```cmdkey /list```
   Use stored credentials with: ```runas /savecred /user:admin cmd.exe```
4. IIS Configuration Files
  Web server configurations may contain stored passwords in web.config:
    - C:\inetpub\wwwroot\web.config
    - C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\web.config
  Search for database credentials: ```type C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\web.config | findstr connectionString```
5. PuTTY Stored Credentials
  PuTTY stores proxy credentials in the registry. Retrieve them with: ```reg query HKEY_CURRENT_USER\Software\SimonTatham\PuTTY\Sessions\ /f "Proxy" /s```
### Q.1: A password for the julia.jones user has been left on the Powershell history. What is the password?

To read the Powershell history enter the following command: ```type $Env:userprofile\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadline\ConsoleHost_history.txt```

![Reading Powershell history](images/julia.jones%20password.png)
There on line 6 we find her password.
#### Answer: ZuperCkretPa5z
### Q.2: A web server is running on the remote host. Find any interesting password on web.config files associated with IIS. What is the password of the db_admin user?
According to the task, there are two possible locations where we can find these web.config files:
- C:\inetpub\wwwroot\web.config
- C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\web.config
The first one does not exist on the system, but if we run the command with the second location we find a connectionString:
```
type C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\web.config | findstr connectionString
```
