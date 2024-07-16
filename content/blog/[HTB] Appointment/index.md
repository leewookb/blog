---
title: "[HTB] Appointment"
author: "Wook Lee"
date: "2024-07-11"
tags: ["Hack The Box"]
---

I just finished Tier 0 of the Starting Point and now I'm moving on to Tier 1 boxes. These are still `very easy` level boxes, but we all start somewhere, and I'm enjoying every step of this process. That's what matters hehe.

![alt text](image.png#center)

---

#### What does the acronym SQL stand for?

```
Structured Query Language
```

<br>

#### What is one of the most common type of SQL vulnerabilities?

```
SQL Injection
```

<br>

#### What is the 2021 OWASP Top 10 classification for this vulnerability?

```
A03:2021-Injection
```

![alt text](image-1.png#center)

<br>

#### What does Nmap report as the service and version that are running on port 80 of the target?

```
Apache httpd 2.4.38 ((Debian))
```

```shell
┌─[us-starting-point-vip-1-dhcp]─[10.10.14.28]─[leewookb@htb-wv2b0ymwtt]─[~/Desktop]
└──╼ [★]$ nmap -sC -sV 10.129.50.1 -p 80
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-07-11 13:16 CDT
Nmap scan report for 10.129.50.1
Host is up (0.0085s latency).

PORT   STATE SERVICE VERSION
80/tcp open  http    Apache httpd 2.4.38 ((Debian))
|_http-server-header: Apache/2.4.38 (Debian)
|_http-title: Login

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 6.76 seconds
```

<br>

#### What is the standard port used for the HTTPS protocol?

```
443
```

<br>

#### What is a folder called in web-application terminology?

```
directory
```

<br>

#### What is the HTTP response code is given for 'Not Found' errors?

```
404
```

<br>

#### Gobuster is one tool used to brute force directories on a webserver. What switch do we use with Gobuster to specify we're looking to discover directories, and not subdomains?

```
dir
```

![alt text](image-2.png#center)

<br>

#### What singlce character can be used to comment out the rest of a line in MySQL?

```
#
```

We can also use `--` for commenting, but that's two characters.

<br>

#### If user input is not handled carefully, it could be interpreted as a comment. Use a comment to login as admin without knowing the password. What is the first word on the webpage returned?

```
Congratulations
```

I first tried `admin#` but kept failing to log in, so since the `gobuster` was mentioned earlier, I tried dirbuster to see if there were any helpful folders open but couldn't find any.

After peeping the provided walkthrough, I learned that I had to include `'` the single quote to close the query, allowing the script to search for the `admin` username and then add the hashtag symbol to comment out the rest of the query.

```shell
┌─[us-starting-point-vip-1-dhcp]─[10.10.14.28]─[leewookb@htb-wv2b0ymwtt]─[~/Desktop]
└──╼ [★]$ gobuster dir -u http://10.129.50.1 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
===============================================================
Gobuster v3.6
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://10.129.50.1
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.6
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
/images               (Status: 301) [Size: 311] [--> http://10.129.50.1/images/]
/css                  (Status: 301) [Size: 308] [--> http://10.129.50.1/css/]
/js                   (Status: 301) [Size: 307] [--> http://10.129.50.1/js/]
/vendor               (Status: 301) [Size: 311] [--> http://10.129.50.1/vendor/]
/fonts                (Status: 301) [Size: 310] [--> http://10.129.50.1/fonts/]
/server-status        (Status: 403) [Size: 276]
Progress: 220560 / 220561 (100.00%)
===============================================================
Finished
===============================================================
```

![alt text](image-3.png#center)

<br>

![alt text](image-4.png#center)

<br>

#### Submit root flag

```
e3d0796d002a446c0e622226f42e9672
```

<br>

![alt text](image-5.png#center)
