---
title: "[HTB] Optimum"
author: "Wook Lee"
date: "2024-08-09"
tags: ["Hack The Box"]
---

<div style="text-align: center"><img src="./giphy.gif"></div>

This machine was pretty straightforward and much easier than I expected it to be. I truly feel like I have improved a lot in the past 2-3 weeks because 2 weeks ago, I definitely would've not been able to solve this machine by myself. I still have a lot of room to develop, and I found myself freezing and getting lost when it came to the **_privilege escalation_** stage. I definitely need to spend more time working on it. Still, I think I deserve to be proud of myself for tackling this machine (almost) entirely on my own.

---

![alt text](image.png#center)
nmap scan showed that only 1 port open, which is the port 80 with the version of **_HttpFileServer httpd 2.3_**

<br>

![alt text](image-1.png#center)
navigated to the web of target ip address **_10.10.10.8_**.

<br>

![alt text](image-2.png#center)
couldn't find anything juicy excpet when I clicked on the **_login_** button there was this pop-up asking for credentials.

<br>

![alt text](image-3.png#center)
I searched on **_searchsploit_** to see if there is an exploit for **_Httpfileserver 2.3_** that I found through an nmap scan and there was one exploit titled **_Rejetto HttpFileserver 2.3.x - Remote Command Execution_**. RCE attack is exactly what I was hoping for.

<br>

![alt text](image-4.png#center)
Since I knew there exists an exploit for the version that's running on the server, I moved on to **_metasploit_**

<br>

![alt text](image-5.png#center)
on the options page of the exploit, configured every required option including **RHOSTS**, **RPORT**, **SRVHOST**, **SRVPORT**, **LHOST**, and **LPORT**.

<br>

![alt text](image-6.png#center)
successfully executed the exploit and gained access to the user shell.

<br>

![alt text](image-7.png#center)
obtained the user flag.

<br>

![alt text](image-8.png#center)
However with the current user privilege, I could not access the root user directory. It was time for privilege escalation to move up vertically.

<br>

![alt text](image-9.png#center)
As I learned from the previous machines, searched for a `post/multi/recon` module and it returned 4 modules. The second module is the one I've used before on a machine which explores the current compromised local session and suggests all the potential exploits.

The **post/multi/recon** represents a category of post-exploitation modules designed to perform reconnaissance tasks on compromised systems.

- **_post_**: short for **_post-exploitation_**. These modules are used after gaining access to a target system. They help gather additional information or achieve specific tasks once the initial compromise has been made.

- **_multi_**: indicates that the module is applicable to multiple platforms or operating systems.

- **_recon_**: short for **_reconnaissance_**, these modules are used to collect information about the target system or network.

<br>

![alt text](image-10.png#center)
As you could see the module requires me to list a session I would like to use the exploit on. In my case, I listed the session that I previously obtained which had id 2.

<br>

![alt text](image-11.png#center)
It outputted all the possible exploits I could run on the current backgrouned session and what I decided to use was named `exploit/windows/local/ms16_032_secondary_logon_handle_privesc`. As you could notice from its name, it is used for a **_privilege escalation_** exactly what I was looking for.

<br>

![alt text](image-12.png#center)
successfully executed the exploit and gained the root shell! as you could see the `getuid` command returned `NT AUTHORITY\SYSTEM`, another name for root!

<br>

![alt text](image-13.png#center)
Got the root flag :)

<br>

![alt text](image-14.png#center)
