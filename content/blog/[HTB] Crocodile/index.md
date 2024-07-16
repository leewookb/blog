---
title: "[HTB] Crocodile"
author: "Wook Lee"
date: "2024-07-13"
tags: ["Hack The Box"]
---

#### What Nmap scanning switch employs the use of default scripts during a scan?

```
-sC
```

<br>

#### What service version is found to be running on port 21?

```
vsftpd 3.0.3
```

```shell
┌──(kali㉿kali)-[~/Desktop]
└─$ nmap -sC -sV 10.129.60.250
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-07-13 00:54 EDT
Nmap scan report for 10.129.60.250
Host is up (0.034s latency).
Not shown: 998 closed tcp ports (conn-refused)
PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.3
| ftp-syst:
|   STAT:
| FTP server status:
|      Connected to ::ffff:10.10.14.28
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 1
|      vsFTPd 3.0.3 - secure, fast, stable
|_End of status
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
| -rw-r--r--    1 ftp      ftp            33 Jun 08  2021 allowed.userlist
|_-rw-r--r--    1 ftp      ftp            62 Apr 20  2021 allowed.userlist.passwd
80/tcp open  http    Apache httpd 2.4.41 ((Ubuntu))
|_http-title: Smash - Bootstrap Business Template
|_http-server-header: Apache/2.4.41 (Ubuntu)
Service Info: OS: Unix

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 8.08 seconds
```

<br>

#### What FTP code is returned to us for the "Anonymous FTP login allowed message?"

```
230
```

![alt text](image.png#center)

<br>

#### After connecting to the FTP server using the ftp client, what username do we provide when prompted to log in anonymously?

```
anonymous
```

![alt text](image-1.png#center)

<br>

#### After connecting to the FTP server anonymously, what command can we use to download the files we find on the FTP server?

```
get
```

![alt text](image-2.png#center)

<br>

#### What is one of the higher-privilege sounding usernames in `allowed.userlist` that we download from the FTP server?

```
admin
```

```shell
┌──(kali㉿kali)-[~/Desktop]
└─$ ls
allowed.userlist  allowed.userlist.passwd  hash-id.py  ntlm_1605054722641.txt  openvpn  pico

┌──(kali㉿kali)-[~/Desktop]
└─$ cat allowed.userlist
aron
pwnmeow
egotisticalsw
admin
```

<br>

#### What version of Apache HTTP Server is running on the target host?

```
Apache httpd 2.4.41
```

![alt text](image-3.png#center)

<br>

#### What switch can we use with Gobuster to specify we are looking for specific filetypes?

```
-x
```

`gobuster dir --help` revealed the `-x` option.

![alt text](image-4.png#center)

<br>

#### Which PHP file can we identify with directory brute force that will provide the opportunity to authenticate to the web service?

```
login.php
```

The following command revealed PHP files with dirbuster on the target host.
`gobuster dir -u http://10.129.60.250 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -x php`

`-u`: url

`-w`: wordlist

`-x`: file types to look for

```shell
┌──(kali㉿kali)-[~/Desktop]
└─$ gobuster dir -u http://10.129.60.250 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -x php

===============================================================
Gobuster v3.6
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url: http://10.129.60.250
[+] Method: GET
[+] Threads: 10
[+] Wordlist: /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
[+] Negative Status codes: 404
[+] User Agent: gobuster/3.6
[+] Extensions: php
[+] Timeout: 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
/.php (Status: 403) [Size: 278]
/login.php (Status: 200) [Size: 1577]
/assets (Status: 301) [Size: 315] [--> http://10.129.60.250/assets/]
/css (Status: 301) [Size: 312] [--> http://10.129.60.250/css/]
/js (Status: 301) [Size: 311] [--> http://10.129.60.250/js/]
/logout.php (Status: 302) [Size: 0] [--> login.php]
/config.php (Status: 200) [Size: 0]
/fonts (Status: 301) [Size: 314] [--> http://10.129.60.250/fonts/]
/dashboard (Status: 301) [Size: 318] [--> http://10.129.60.250/dashboard/]
```

<br>

#### Submit root flag

```
c7110277ac44d78b6a9fff2232434d16
```

From the earlier task, we downloaded two files from the ftp server:

1. **_allowed.userlist_**
2. **_allowed.userlist.passwd_**

and we confirmed there was the username, `admin`. As you can see from the passwd file, the password for `admin` is `rKXM59ESxesUFHAd`.

![alt text](image-6.png#center)

![alt text](image-7.png#center)

<br>

From the gobuster output, we revealed there was `/login.php` page, so I navigated to the page and entered the credentials we had found from the ftp server and finally found the flag.

![alt text](image-5.png#center)

![alt text](image-8.png#center)

<br>

![alt text](image-9.png#center)
