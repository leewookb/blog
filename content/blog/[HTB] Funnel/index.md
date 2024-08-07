---
title: "[HTB] Funnel"
author: "Wook Lee"
date: "2024-07-18"
tags: ["Hack The Box"]
---

#### How many TCP ports are open?

```
2
```

According to output of the nmap scan, there are 2 open ports.

![alt text](image.png#center)

<br>

#### What is the name of the directory that is available on the FTP server?

```
mail_backup
```

```shell
PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.3
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_drwxr-xr-x    2 ftp      ftp          4096 Nov 28  2022 mail_backup
```

<br>

#### What is the default account password that every new member on the "Funnel" team should change as soon as possible?

```
funnel123#!#
```

```shell
┌──(kali㉿kali)-[~/Desktop]
└─$ ftp 10.129.151.228
Connected to 10.129.151.228.
220 (vsFTPd 3.0.3)
Name (10.129.151.228:kali): anonymous
331 Please specify the password.
Password:
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> ls
229 Entering Extended Passive Mode (|||32449|)
150 Here comes the directory listing.
drwxr-xr-x    2 ftp      ftp          4096 Nov 28  2022 mail_backup
226 Directory send OK.
ftp> cd mail_backup
250 Directory successfully changed.
ftp> ls
229 Entering Extended Passive Mode (|||61475|)
150 Here comes the directory listing.
-rw-r--r--    1 ftp      ftp         58899 Nov 28  2022 password_policy.pdf
-rw-r--r--    1 ftp      ftp           713 Nov 28  2022 welcome_28112022
226 Directory send OK.
```

<br>

After logged into the ftp server as `anonymous`, I was able to locate 2 files inside the mail_backup directory. Downloaded those files to local and the `password_policy.pdf` file disclosed the default password of a newly created user.

![alt text](image-1.png#center)

<br>

#### Which user has not changed their default password yet?

```
christine
```

From the `welcome_28112022` file, we noticed that the new users are 5 people: `optimus`, `albert`, `andreas`, `christine`, and `maria`. As we already saw from the previous task, the default password was `funnel#!#` for new users. We can try to login with their credentials one by one to ssh server and see if any of them has not changed their default password.

![alt text](image-2.png#center)

I was able to login to the ssh server with `christine`'s credentials with the default password.
![alt text](image-3.png#center)

---

Instead of manually inputting password for each credential, we can also use `Hydra` to automate the process.

![alt text](image-6.png#center)

<br>

#### Which service is running on TCP port 5432 and listens only on localhost?

```
postgresql
```

First, I ran another nmap scan particularly on the port 5432 and it showed that `postgresql` as the servic running on the port 5432.

```shell
┌──(kali㉿kali)-[~/Desktop]
└─$ nmap -sC -sV 10.129.151.228 -p 5432
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-07-18 00:52 EDT
Nmap scan report for 10.129.151.228
Host is up (0.033s latency).

PORT     STATE  SERVICE    VERSION
5432/tcp closed postgresql

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 0.29 seconds
```

<br>

To confirm I used the `ss` command to dump socket statistics. It's a utility to investigate sockets, similar to the older `netstat` command.

- `-t` or `--tcp`: this option displays only TCP sockets.
- `-l` or `--listening`: displays only listening sockets.

```shell
christine@funnel:~$ ss -tl sport = :5432
State            Recv-Q           Send-Q                       Local Address:Port                             Peer Address:Port           Process
LISTEN           0                4096                             127.0.0.1:postgresql                            0.0.0.0:*
```

<br>

#### Since you can't access the previously mentioned service from the local machine, you will have to create a tunnel and connect to it from your machine. What is the correct type of tunneling to use? remote port forwarding or local port forwarding?

```
local port forwarding
```

According to the definition of tunneling, one can use it to access resources that are available only to internal networks. To create/facilitate such tunnels, an appropriate application should be used. The most known one is `SSH`.

The `SSH` protocol is vastly used for maintaining and accessing remote systems in a secure and encrypted way. But, it also offers the possibility to create tunnels that operate over the SSH protocol. More specifically, SSH offers various types of tunnels.

When `local port forwarding` is used, a separate tunnel is created inside the existing valid SSH session that forwards network traffic from a local port on the client's machine over to the remote server's port. Under the hood, SSH allocates a socket listener on the client on the given port.

When `remote port forwarding`, also known as `Reverse Tunneling` is used, after a successful SSH connection, a separate tunnel is created which SSH users to redirect incoming traffic to the server's port back to the client. Internally, SSH allocates a socket listener on the server on the given port. When a connection is made to this port, the connection is forwarded over the existing SSH session over to the local client's port.

#### Again in simple terms

##### Local Port Forwarding

Imagine you have a computer at home, and there's a service or website you can only access from your office network. You want to connect to that service from your home computer. Local port forwarding can help you do that.

How it works:

1. You create a tunnel (connection) from your home computer to your office computer
2. You tell your home computer to listen on a specific port (e.g., 8080).
3. Any data you send to this port on your home computer gets forwarded to the service on your office network.

##### Remote Port Forwarding

Now imagine the opposite scenario: you are at work, and you want to access a service running on your home computer, but your home network doesn't allow incoming connections from outside. Remote port forwarding can help here.

How it works:

1. You create a tunnel from your office computer to your home computer.
2. You tell your home computer to listen on a specific port and forward requests to a service on your office computer.
3. Any data sent to this port on your home computer gets forwarded to the service on your office network.

#### Tunneling

To use local port forwarding with SSH, you can use the `ssh` command with the `-L` option, followed by the local port, remote host and port, and the remote SSH server.

`ssh -L 1234:localhost:22 christine@10.129.151.228`

When you run this command, the SSH client will establish a secure connection to the remote SSH server, and it will listen for incoming connections on the local port `1234`. When a client connects to the local port, the SSH client will forward the connection to the remote server on port `22`. This allows the local client to access services on the remote server as if they were running on the local machine.

However, we want to forward traffic from any given local port, for instance `1234`, to the port on which `PostgreSQL` is listening, `5432`, on the remote server. Therefore specify port 1234 to the left of localhost, and 5432 to the right, indicating the target port.

`ssh -L 1234:localhost:5432 christine@10.129.151.228`

<br>

#### What is the name of the database that holds the flag?

```
Secrets
```

After entering christine's password, we have a shell on the target system like we did before, however, under the hood, SSH has opened up a socket on our local machine on port 1234, to which we can now direct traffic that we want forwarded to port 5432 on the target machine.

By running the following command, I was able to interact with the postgresql service running locally on the target machine.

`psql -U chirstine -h localhost -p 1234`

Then `\l` command to list all the databases. Out of the databases, the database with the name **\*Secrets** is very suspicious. I used `\c secrets` to change the database to the secrets db and `\dt` command to display all the tables that belong to the db. Finally `SELECT * FROM flag;` to display the flag.

![alt text](image-4.png#center)

<br>

#### Could you use a dynamic tunnel instead of local port forwarding? yes or No.

```
Yes
```

In many cases, the local port forwarding tunnel can indeed be replaced by a dynamic one.

<br>

#### Submit root flag

```
cf277664b1771217d7006acdea006db1
```

<br>

![alt text](image-5.png#center)
