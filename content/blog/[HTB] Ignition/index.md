---
title: "[HTB] Ignition"
author: "Wook Lee"
date: "2024-07-15"
tags: ["Hack The Box"]
---

#### Which service version is found to be running on port 80?

```
nginx 1.14.2
```

![alt text](image.png#center)

<br>

#### What is the 3-digit HTTP status code returned when you visit http://{machine IP}/?

```
302
```

`curl -v http://10.129.98.187`

![alt text](image-1.png#center)

<br>

#### What is the virtual host name the webpage expects to be accessed by?

```
ignition.htb
```

![alt text](image-2.png#center)

<br>

#### What is the full path to the file on a Linux computer that holds a local list of domain name to IP address pairs?

```
/etc/hosts
```

the `ignition.htb` address and its corresponding IP address is added to the file as an entry

![alt text](image-3.png#center)

<br>

#### Use a tool to brute force directories on the webserver. What is the full URL to the Magento login page?

```
http://ignition.htb/admin
```

I ran a gobuster to brute force directories on the web server and found that `/admin` url navigates us to the Magento login page.

`gobuster dir -u http://ignition.htb -w /usr/share/dirbuster/wordlists/directory-list-2.3-medium.txt`

![alt text](image-4.png#center)

![alt text](image-5.png#center)

<br>

#### Look up the password requirements for Magento and also try searching for the most common passwords of 2023. Which password provides access to the admin account?

```
qwerty123
```

As you can see from the search result, Magento requires a minimum of seven chracters (both letters and numbers). From the list of most common passwords of 2023, only passwords that contain both letters and numbers are: `qwerty123` and `1q2w3e`. However, the latter has only 6 characters.

`username: admin, password: qwerty123`. The pair worked!

![alt text](image-6.png#center)

<br>

![alt text](image-7.png#center)

<br>

![alt text](image-8.png#center)

<br>

#### Submit root flag

```
797d6c988d9dc5865e010b9410f247e0
```

I've tried looking everywhere on the web page. It turned out the flag was on the main dashboard page. haha

![alt text](image-10.png#center)

<br>

![alt text](image-9.png#center)
