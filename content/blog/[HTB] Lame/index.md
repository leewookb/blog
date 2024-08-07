---
title: "[HTB] Lame"
author: "Wook Lee"
date: "2024-07-28"
tags: ["Hack The Box"]
---

<div style="text-align: center"><img src="./giphy.gif"></div>

This was my first time attempting a Hack The Box machine that's not on the Starting Point. The machine offers two modes: **_Adventure Mode_** and **_Guided Mode_**. I first went for the Adventure Mode because I wanted to explore the box and solve it on my own. However, I hit a dead end and didn't know how to proceed. So, I switched to Guided Mode, which was similar to the Starting Point boxes. With the help of Guided Mode and the questions, I was able to solve the box easily. Looking back, it wasn't really that hard, but I felt stuck and had no clude what to do next. It takes a lot of trial and error to get better at this, and I've improved a lot over the past few months. I'm proud of my progress, but I know I have a long way to go. In the end, it's so much fun, and that's what really matters.

---

#### How many of the nmap top 1000 TCP ports are open on the remote host?

```
4
```

![alt text](image-4.png#center)

I scanned all 65535 ports so I found 5 open ports but the question was asking for the top 1000 TCP ports. Excluding the port 3632, the answer should be 4.

<br>

#### What version of VSFTPd is running on Lame?

```
2.3.4
```

![alt text](image-5.png#center)

<br>

#### There is a famous backdoor in VSFTPd version 2.3.4, and a Metasploit module to exploit it. Doe sthat exploit work here?

```
no
```

I've set up the required `RHOSTS` and `RPORT`, then ran the exploit but didn't seem to work.

![alt text](image-6.png#center)
![alt text](image-7.png#center)
![alt text](image-8.png#center)

<br>

#### What version of Samba is running on Lame? Give the numbers up to but not including "-Debian".

```
3.0.20
```

From the nmap scan, we can see that it's running the version 3.0.20 of Samba.

![alt text](image-9.png#center)

<br>

#### What 2007 CVE allows for remote code execution in this version of Samba via shell metacharacters involving the `SamrChangePassword` function when the "username map script" option is enabled in `smb.conf`?

```
CVE-2007-2447
```

![alt text](image-10.png#center)

<br>

#### Exploiting CVE-2007-2447 returns a shell as which user?

```
root
```

![alt text](image-11.png#center)

![alt text](image-12.png#center)

![alt text](image-13.png#center)

<br>

#### Submit the flag located in the makis user's home directory.

```
063d439bcae7f5e0ab82493bd8035243
```

![alt text](image-14.png#center)

<br>

#### Submit the flag located in root's home directory.

```
910d82f873622fdf9e5abb95ba010832
```

![alt text](image-15.png#center)

<br>

![alt text](image-16.png#center)
