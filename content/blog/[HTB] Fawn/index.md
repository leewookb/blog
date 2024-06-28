---
title: "[HTB] Fawn"
author: "Wook Lee"
date: "2024-06-17"
---

> Hopefully one day I will become very good at CTFs.

##### TASK 1

What does the 3-letter acronym FTP stand for?

File Transfer Protocol
TASK 2

Which port does the FTP service listen on usually?

21
TASK 3

What acronym is used for the secure version of FTP?

SFTP
Secure File Transfer Protocol. Designed by the Internet Engineering Task Force as an extension of Secure Shell (SSH).

TASK 4

What is the command we can use to send an ICMP echo request to test our connection to the target?

ping
┌─[us-starting-point-vip-1-dhcp]─[10.10.14.27]─[leewookb@htb-psvucohdrg]─[~]
└──╼ [★]$ ping 10.129.67.107
PING 10.129.67.107 (10.129.67.107) 56(84) bytes of data.
64 bytes from 10.129.67.107: icmp_seq=1 ttl=63 time=8.68 ms
64 bytes from 10.129.67.107: icmp_seq=2 ttl=63 time=8.81 ms
64 bytes from 10.129.67.107: icmp_seq=3 ttl=63 time=8.87 ms
64 bytes from 10.129.67.107: icmp_seq=4 ttl=63 time=8.68 ms
^C
--- 10.129.67.107 ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3006ms
rtt min/avg/max/mdev = 8.679/8.760/8.873/0.082 ms
┌─[us-starting-point-vip-1-dhcp]─[10.10.14.27]─[leewookb@htb-psvucohdrg]─[~]
└──╼ [★]$
TASK 5

From your scans, what version is FTP running on the target?

vsftpd 3.0.3
┌─[us-starting-point-vip-1-dhcp]─[10.10.14.27]─[leewookb@htb-psvucohdrg]─[~]
└──╼ [★]$ nmap -sV 10.129.67.107
Starting Nmap 7.93 ( https://nmap.org ) at 2024-06-17 05:49 BST
Nmap scan report for 10.129.67.107
Host is up (0.027s latency).
Not shown: 999 closed tcp ports (conn-refused)
PORT STATE SERVICE VERSION
21/tcp open ftp vsftpd 3.0.3
Service Info: OS: Unix

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 0.60 seconds
┌─[us-starting-point-vip-1-dhcp]─[10.10.14.27]─[leewookb@htb-psvucohdrg]─[~]
└──╼ [★]$
TASK 6

From your scans, what OS type is running on the target?

Unix
TASK 7

What is the command we need to run in order to display the 'ftp' client help menu?

ftp -h
TASK 8

What is username that is used over FTP when you want to log in without having an account?

anonymous
┌─[us-starting-point-vip-1-dhcp]─[10.10.14.27]─[leewookb@htb-psvucohdrg]─[~]
└──╼ [★]$ ftp 10.129.67.107
Connected to 10.129.67.107.
220 (vsFTPd 3.0.3)
Name (10.129.67.107:root): anonymous
331 Please specify the password.
Password:
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp>
TASK 9

What is the response code we get for the FTP message 'Login successful'?

230
230 Login successful.
TASK 10

There are a couple of commands we can use to list the files and directories available on the FTP server. One is dir. What is the other that is a common way to list files on a Linux system.

ls
┌─[us-starting-point-vip-1-dhcp]─[10.10.14.27]─[leewookb@htb-psvucohdrg]─[~]
└──╼ [★]$ ftp 10.129.67.107
Connected to 10.129.67.107.
220 (vsFTPd 3.0.3)
Name (10.129.67.107:root): anonymous
331 Please specify the password.
Password:
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> ls
200 PORT command successful. Consider using PASV.
150 Here comes the directory listing.
-rw-r--r-- 1 0 0 32 Jun 04 2021 flag.txt
226 Directory send OK.
ftp>
TASK 11

What is the command used to download the file we found on the FTP server?

get
ftp> get flag.txt
local: flag.txt remote: flag.txt
200 PORT command successful. Consider using PASV.
150 Opening BINARY mode data connection for flag.txt (32 bytes).
226 Transfer complete.
32 bytes received in 0.00 secs (32.7225 kB/s)
ftp>
SUBMIT THE FLAG

Submit root flag

035db21c881520061c53e0536e44f815
┌─[us-starting-point-vip-1-dhcp]─[10.10.14.27]─[leewookb@htb-psvucohdrg]─[~]
└──╼ [★]$ ls
Desktop flag.txt my_data Templates
┌─[us-starting-point-vip-1-dhcp]─[10.10.14.27]─[leewookb@htb-psvucohdrg]─[~]
└──╼ [★]$ cat flag.txt
035db21c881520061c53e0536e44f815
┌─[us-starting-point-vip-1-dhcp]─[10.10.14.27]─[leewookb@htb-psvucohdrg]─[~]
└──╼ [★]$
