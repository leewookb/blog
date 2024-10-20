---
title: "[HTB] Grandpa"
author: "Wook Lee"
date: "2024-08-10"
tags: ["Hack The Box"]
---

![alt text](image-1.png#center)
**_nmap_** scan result shows that there is one open port, which is the port **80** and the version it's running is is **_Microsoft IIS httpd 6.0_**.

<br>

![alt text](image.png#center)
Nothing interesting on the web page of the target ip address.

<br>

![alt text](image-2.png#center)
I searched for a known exploit of the **_Microsoft IIS 6.0_** version with **_Searchsploit_**.

<br>

![alt text](image-3.png#center)
**_CVE-2017-7269_**, also known as the **_ScStoragePathFromUrl_** exploit seemed like a good choice in this case. Under its description it said it **_allowes remote attackers to execute arbitrary code_**. That's exactly what I was interested in.

<br>

![alt text](image-4.png#center)

<br>

![alt text](image-5.png#center)
looking up the CVE on the msfconsole.

<br>

![alt text](image-6.png#center)
configuring the options of the exploit.

<br>

![alt text](image-7.png#center)
The exploit successfully ran and I got the meterpreter shell.

<br>

![alt text](image-8.png#center)
However, the current user's capability was very limited that I was not able to enumerate any information. Definitely in need for a **_privilege escalation_**.

<br>

![alt text](image-9.png#center)
In previous machines, I remember using the **_local_exploit_suggester_** module located under **_post/multi/recon_** for post-exploitation.

<br>

![alt text](image-10.png#center)
Out of the many exploits it returned, there were six **potentially vulnerable** exploits I could use. I decided to use the **_ms10_015_kitrap0d_** exploit because I had used it before.

<br>

![alt text](image-11.png#center)

<br>

![alt text](image-12.png#center)
Contrary to my expectations, the exploit did not work. The error kept returning **_"Access is denied"_**.

<br>

![alt text](image-13.png#center)
Tried another exploit and the result was the same.

<br>

![alt text](image-14.png#center)
Back to the meterpreter shell, I ran the **ps** command to see what processes are running on the target system. The current process that our **meterpreter payload** is running on is the **_rundll32.exe_** and its **PID** is 3724. As you could see from the screenshot above, the **_User_** column of the process is empty, meaning the payload has no idea who the user is.

In order to stabilize and properly run the post-exploit, I decided to migrate from the current process to another process which has the **_admin user_**.

<br>

![alt text](image-15.png#center)
Migrated from the pid 3724 to 1880.

<br>

![alt text](image-16.png#center)
After migrating to another process, I backgrounded the session again to carry out another attempt at privilege escalation. This time I used another exploit named **_ms14_070_tcpip_ioctl_**.

<br>

![alt text](image-17.png#center)
Finally the exploitation was successful and all commands worked properly without error.

<br>

![alt text](image-18.png#center)
got the user flag.

<br>

![alt text](image-19.png#center)
got the root flag.

<br>

![alt text](image-20.png#center)
