---
title: "[HTB] Blue"
author: "Wook Lee"
date: "2024-08-06"
tags: ["Hack The Box"]
---

<div style="text-align: center"><img width="400px"src="./giphy.gif"></div>

This is my first post in a week, not because I was procastinating or slacking off, but because I was taking time to watch pentesting lectures to strengthen my foundational knowledge. I've alwas felt my foundation wa a bit weak. After watching some videos, I tackled this box, one of the easy-level challenges they recommended. I'm proud to say that, for the first time, I solved it without any help from a walkthrough. Even though it was a straightforward box, solving it on my own is really satisfying. I know there's still a lot of room for improvement, especially in writing better write-ups but still, I'm definitely making progress!

---

<br>

As always, I started the enumeration phase by scanning ports with **_nmap_**.

```shell
# Nmap 7.94SVN scan initiated Tue Aug  6 23:39:01 2024 as: nmap -T4 -A --min-rate 3000 -p- -oA output 10.10.10.40
Nmap scan report for 10.10.10.40
Host is up (0.0084s latency).
Not shown: 65526 closed tcp ports (reset)
PORT      STATE SERVICE      VERSION
135/tcp   open  msrpc        Microsoft Windows RPC
139/tcp   open  netbios-ssn  Microsoft Windows netbios-ssn
445/tcp   open  microsoft-ds Windows 7 Professional 7601 Service Pack 1 microsoft-ds (workgroup: WORKGROUP)
49152/tcp open  msrpc        Microsoft Windows RPC
49153/tcp open  msrpc        Microsoft Windows RPC
49154/tcp open  msrpc        Microsoft Windows RPC
49155/tcp open  msrpc        Microsoft Windows RPC
49156/tcp open  msrpc        Microsoft Windows RPC
49157/tcp open  msrpc        Microsoft Windows RPC
No exact OS matches for host (If you know what OS is running on it, see https://nmap.org/submit/ ).
TCP/IP fingerprint:
OS:SCAN(V=7.94SVN%E=4%D=8/6%OT=135%CT=1%CU=31869%PV=Y%DS=2%DC=T%G=Y%TM=66B2
OS:FAC2%P=x86_64-pc-linux-gnu)SEQ(SP=107%GCD=1%ISR=107%TI=I%CI=I%II=I%SS=S%
OS:TS=7)OPS(O1=M53CNW8ST11%O2=M53CNW8ST11%O3=M53CNW8NNT11%O4=M53CNW8ST11%O5
OS:=M53CNW8ST11%O6=M53CST11)WIN(W1=2000%W2=2000%W3=2000%W4=2000%W5=2000%W6=
OS:2000)ECN(R=Y%DF=Y%T=80%W=2000%O=M53CNW8NNS%CC=N%Q=)T1(R=Y%DF=Y%T=80%S=O%
OS:A=S+%F=AS%RD=0%Q=)T2(R=Y%DF=Y%T=80%W=0%S=Z%A=S%F=AR%O=%RD=0%Q=)T3(R=Y%DF
OS:=Y%T=80%W=0%S=Z%A=O%F=AR%O=%RD=0%Q=)T4(R=Y%DF=Y%T=80%W=0%S=A%A=O%F=R%O=%
OS:RD=0%Q=)T5(R=Y%DF=Y%T=80%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)T6(R=Y%DF=Y%T=80%W
OS:=0%S=A%A=O%F=R%O=%RD=0%Q=)T7(R=Y%DF=Y%T=80%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)
OS:U1(R=Y%DF=N%T=80%IPL=164%UN=0%RIPL=G%RID=G%RIPCK=G%RUCK=G%RUD=G)IE(R=Y%D
OS:FI=N%T=80%CD=Z)

Network Distance: 2 hops
Service Info: Host: HARIS-PC; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
|_clock-skew: mean: -19m57s, deviation: 34m36s, median: 0s
| smb2-time:
|   date: 2024-08-07T04:40:29
|_  start_date: 2024-08-07T04:29:18
| smb-security-mode:
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
| smb-os-discovery:
|   OS: Windows 7 Professional 7601 Service Pack 1 (Windows 7 Professional 6.1)
|   OS CPE: cpe:/o:microsoft:windows_7::sp1:professional
|   Computer name: haris-PC
|   NetBIOS computer name: HARIS-PC\x00
|   Workgroup: WORKGROUP\x00
|_  System time: 2024-08-07T05:40:30+01:00
| smb2-security-mode:
|   2:1:0:
|_    Message signing enabled but not required

TRACEROUTE (using port 22/tcp)
HOP RTT     ADDRESS
1   8.08 ms 10.10.14.1
2   8.47 ms 10.10.10.40

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Tue Aug  6 23:40:34 2024 -- 1 IP address (1 host up) scanned in 93.01 seconds
```

<br>

Since we know the target host is running the smb server, I tried the following command `smbclient -L \\\\10.10.10.40\\` to list the shares
![alt text](image.png#center)

<br>

Anonymous login was failed with every share but the **_Users_** share. I spent time looking around inside the share but there wasn't any useful information.
![alt text](image-1.png#center)

<br>

While exploring the **_Users_** share, ran another nmap script to find if there's any smb vulnerabilities. The scan found one vulnerability **CVE-2017-0143** also known as **ms17-010**.
![alt text](image-2.png#center)

<br>

To exploit the found vulnerability, I came to **_msfconsole_** and searched for **ms17-010**.
![alt text](image-3.png#center)

<br>

Configure options of exploit. set **RHOSTS** to 10.10.10.40 and **LHOST** to 10.10.14.15
![alt text](image-4.png#center)

<br>

Successfully got the shell!
![alt text](image-5.png#center)

<br>

From the user, Haris' desktop, the user flag could be found.
![alt text](image-6.png#center)

<br>

Moved up to **All Users** directory, then to **Administrator** directory, there was the root.txt.
![alt text](image-7.png#center)

<br>

![alt text](image-8.png#center)
