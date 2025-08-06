## Cron jobs execute scripts at scheduled times with the owner’s privileges. While not inherently vulnerable, they can be exploited for privilege escalation if a root-owned cron job runs a modifiable script.

- Crontabs store cron job configurations, with system-wide jobs located in /etc/crontab.
- Finding a root-owned cron job that executes a modifiable script allows attackers to inject a reverse shell.
- Attackers use available system tools (e.g., nc) to establish a root shell on an attacking machine.
- Common misconfigurations in companies:
  - Admins schedule cron jobs but forget to remove them after deleting scripts.
  - If the script’s full path isn’t specified, attackers can place a malicious script in a searchable path (e.g., home directory).
    Some tools in scripts (e.g., tar, 7z, rsync) can be exploited using wildcard vulnerabilities.
- Always check crontabs, as they can provide easy privilege escalation opportunities, especially in poorly maintained systems.
