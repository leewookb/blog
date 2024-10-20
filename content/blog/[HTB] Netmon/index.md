---
title: "[HTB] Netmon"
author: "Wook Lee"
date: "2024-08-12"
tags: ["Hack The Box"]
---

![alt text](image.png#center)

As usual, I started with an **_nmap_** scan. There were a lot of open ports, but what immediately caught my eye was the **_FTP_** server. From the screenshot above, you can see that the **_Anonymous FTP login_** was allowed, and it appeared that the C drive directory accessible, which I could potentially get a lot of important information from.

<br>

![alt text](image-1.png#center)
As expected, I was able to obtain the user flag immediately after accessing the FTP server.

<br>

![alt text](image-2.png#center)
Got the user flag.

<br>

![alt text](image-3.png#center)
I navigated to the web page of the target address and it's titled **PRTG Network Monitor**.

<br>

![alt text](image-4.png#center)
I googled **"PRTG Network Monitor default credentials"** and found the credentials above, **prtgadmin:prtgadmin**. However, they did not work.

<br>

![alt text](image-5.png#center)
Also googled **"PRTG Network Monitor exploit"** and found the exploit above. But as you can see, it requires **authenticated** meaning I had to find the password first in order to run the exploit.

<br>

![alt text](image-7.png#center)
While I was researching about the **PRTG Network Monitor** I found an interesting information about where it stores its data. The path of the default data folder is the following: `%programdata%\Paessler\PRTG Network Monitor`.

<br>

![alt text](image-6.png#center)
I **cd** into the location path as learned and I found 3 **PRTG Configuration** files. Downloaded all of the 3 files using the command `get`.

<br>

![alt text](image-8.png#center)

<br>

![alt text](image-9.png#center)
The command **md5sum** returned that the hash of the first two files are identical meaning the contents inside those files are exactly the same. So I just needed to check either of the files and the **PRTG Configuration.old.bak** file.

<br>

![alt text](image-10.png#center)
Inside the first file I checked, the password was encrypted.

<br>

![alt text](image-11.png#center)
Inside the **PRTG Configuration.old.bak** file, I found the credentials.

**Username: prtgadmin**, **Password: PrTg@dmin2018**

<br>

![alt text](image-12.png#center)
Navigated back to the webpage to confirm if the credentials that I obtained actually work, and they did.

<br>

![alt text](image-13.png#center)
Under the exploit-db page that I found earlier, there was this piece of information that instructed me to obtain the **cookie** info use it with the script and it will create a new user **'pentest'** in the administrators group with the password '**P3nT3st!**'.

<br>

![alt text](image-14.png#center)
I powered on the **_burpsuite_** to intercept the traffic and get the cookie information.

<br>

![alt text](image-16.png#center)

`./prtg-exploit.sh -u http://10.10.10.152 -c "_ga=GA1.4.620613745.1723521193; _gid=GA1.4.536926311.1723521193; OCTOPUS1813713946=ezYyRkZGRkIyLTg4MTYtNEU1NC1BQ0U3LTQ1MjdCRUY1QzI1QX0%3D; _gat=1"`

Once I grabbed the cookie information, I replaced the placeholder and ran the above script.

<br>

![alt text](image-17.png#center)
The exploit successfully created new user **'pentest'** with password **'P3nT3st!'** just as expected!

<br>

![alt text](image-18.png#center)
I could have used the **msfconsole** for getting the shell but I wanted to practice another way around and I learned about **_Impacket_**. It is a collection of Python classes for working with network protocols and of course used by pentesters.

<br>

![alt text](image-20.png#center)
Installed the **_impacket-scripts_** to use **psexec.py** file. The **_psexec.py_** is often used during penestration tests to gain remote command execution on target systems once valid credentials are obtained. It can also help in lateral movement within a network by allowing attackers to execute commands on multiple machines using stolen or cracked credentials.

<br>

![alt text](image-19.png#center)
**psexec.py** script with the obtained credentials got me the root administrator shell.

<br>

![alt text](image-21.png#center)
Got the root flag!

<br>

![alt text](image-22.png#center)

<br>
