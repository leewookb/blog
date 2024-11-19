---
title: "[OverTheWire] Bandit 0-17"
author: "Wook Lee"
date: "2024-08-14"
tags: ["Over The Wire", "Bandit"]
---

#### Level 0

The goal of this level is for you to log into the game using SSH. The host to which you need to connect is bandit.labs.overthewire.org, on port 2220. The username is bandit0 and the password is bandit0. Once logged in, go to the Level 1 page to find out how to beat Level 1.

`ssh bandit0@bandit.labs.overthewire.org -p 2220`

---

#### Level 0 -> 1

The password for the next level is stored in a file called readme located in the home directory. Use this password to log into bandit1 using SSH. Whenever you find a password for a level, use SSH (on port 2220) to log into that level and continue the game.

`ssh bandit1@bandit.labs.overthewire.org -p 2220`

![alt text](image.png#center)

**_password_**: `ZjLjTmM6FvvyRnrb2rfNWOZOTa6ip5If`

---

#### Level 1 -> 2

The password for the next level is stored in a file called "-" located in the home directory

![alt text](image-1.png#center)

**_password_**: `263JGJPfgU6LtdEvgfWU1XP5yac29mFx`

---

#### Level 2 -> 3

The password for the next level is stored in a file called spaces in this filename located in the home directory

![alt text](image-2.png#center)

**_password_**: `MNk8KNH3Usiio41PRUEoDFPqfxLPlSmx`

---

#### Level 3 -> 4

The password for the next level is stored in a hidden file in the inhere directory.

![alt text](image-3.png#center)

**_password_**: `2WmrDFRmJIq3IPxneAaMGhap0pFhF3NJ`

---

#### Level 4 -> 5

The password for the next level is stored in the only human-readable file in the inhere directory. Tip: if your terminal is messed up, try the “reset” command.

![alt text](image-5.png#center)

The `file ./*` command returned that the **file#7** has a different file format from the rest of the files. The **_ASCII text_** is also a human-readable file format.

**_password_**: `4oQYVPkxZOOEOO5pTW81FB8j8lxXGUQw`

---

#### Level 5 -> 6

The password for the next level is stored in a file somewhere under the inhere directory and has all of the following properties:

- human-readable
- 1033 bytes in size
- not executable

![alt text](image-6.png#center)

<br>

![alt text](image-7.png#center)
The reason I appended **'c'** after 1033 could be found under the **find**'s **man** page.

<br>

**_password_**: `HWasnPhtq9AVKe0dmk45nxy20cvUa6EG`

---

#### Level 6 -> 7

The password for the next level is stored somewhere on the server and has all of the following properties:

- owned by user bandit7
- owned by group bandit6
- 33 bytes in size

![alt text](image-8.png#center)
There wasn't any directory or file inside the **bandit6** directory.

<br>

![alt text](image-9.png#center)
So I moved up a directory and run the following command: `find /home/ -user bandit7 -group bandit6 -size 33c`. The command returned some outputs but I did not have a permission to access any of them. So I decided to navigate to the parent directory of the current working directory once again.

<br>

![alt text](image-10.png#center)
The code returned too many outputs that I did not want to waste my time reading one by one to find the answer so I appended the following code to only return ones without errors. `2>/dev/null`.

<br>

![alt text](image-11.png#center)
Got the password!

<br>

**_password_**: `morbNTDkSW6jIlUc0ymOdMaLnOlFVAaj`

---

#### Level 7 -> 8

The password for the next level is stored in the file data.txt next to the word millionth

![alt text](image-12.png#center)

**_password_**: `dfwvzFQi4mU0wfNbFOe9RoWskMLg7eEc`

---

#### Level 8 -> 9

The password for the next level is stored in the file data.txt and is the only line of text that occurs only once

![alt text](image-13.png#center)

![alt text](image-14.png#center)

![alt text](image-15.png#center)

**_password_**: `4CKMh1JI91bUIZZPXDqGanal4xvAg0JM`

---

#### Level 9 -> 10

The password for the next level is stored in the file data.txt in one of the few human-readable strings, preceded by several ‘=’ characters.

![alt text](image-16.png#center)

![alt text](image-17.png#center)

**_password_**: `FGUW5ilLVJrxX9kMYMmlN4MgbpfMiqey`

---

#### Level 10 -> 11

The password for the next level is stored in the file data.txt, which contains base64 encoded data

![alt text](image-18.png#center)

**_password_**: `dtR173fZKb0RRsDFSGsg2RWnpNVj3qRr`

---

#### Level 11 -> 12

The password for the next level is stored in the file data.txt, where all lowercase (a-z) and uppercase (A-Z) letters have been rotated by 13 positions.

![alt text](image-19.png#center)
We have to first understand what **ROT13** is before we solve this question. According to the **_Wikipedia_**, **ROT13** is a simple letter subsitution cipher that replaces a letter with the 13th letter after it in the Latin alphabet.

<br>

![alt text](image-20.png#center)
`tr 'A-Za-z' 'N-ZA-Mn-za-m'`: This command tells **tr** to replace each letter in the first set (`A-Za-z`) with the corresponding letter in the second set (`N-ZA-Mn-za-m`), effectively rotating the alphabet by 13 positions.

<br>

**_password_**: `7x16WNeHIi5YkIhWsfFIqoognUTyj9Q4`

---

#### Level 12 -> 13

The password for the next level is stored in the file data.txt, which is a hexdump of a file that has been repeatedly compressed. For this level it may be useful to create a directory under /tmp in which you can work. Use mkdir with a hard to guess directory name. Or better, use the command “mktemp -d”. Then copy the datafile using cp, and rename it using mv (read the manpages!)

![alt text](image-21.png#center)
While researching about this question, I kept encountering the command `xxd` and I learned the following:
The `xxd` command in linux is a powerful utility for creating a hexadecimal (**hex**) dump of a given file. **_It also allows you to reverse the operation, converting a hex dump back into the original binary form_**.

<br>

`-r` is the Reverse operation.

![alt text](image-22.png#center)
**_Permission denied_** because the guest user **bandit12** does not have the permission to **write** in the current directory.

<br>

![alt text](image-23.png#center)
as suggested in the question, I will create the original file inside the **tmp** file since anyone can write inside the file.

<br>

![alt text](image-24.png#center)

![alt text](image-25.png#center)
Decompressed the hexdump file and it's now **gzip** compressed data. I renamed the file as `convert_back.gz` and decompressed it with the following command: `gunzip convert_back.gz`.

It now became a `bzip2` compressed data.

<br>

![alt text](image-26.png#center)
decompressed back the file using `bunzip2` command and once again it's a **gzip** file.

<br>

![alt text](image-27.png#center)
To decompress and extract the contents of a **POSIX tar archive file**, you can use the `tar` command.

<br>

![alt text](image-28.png#center)
From there the process of decompressing files continued until the file format turned out to be **ASCII text**.

<br>

**_Password_**: `FO5dwFsc0cbaIiH0h8J2eUks2vdTDwAn`

---

#### Level 13 -> 14

The password for the next level is stored in /etc/bandit_pass/bandit14 and can only be read by user bandit14. For this level, you don’t get the next password, but you get a private SSH key that can be used to log into the next level. Note: localhost is a hostname that refers to the machine you are working on

![alt text](image-29.png#center)
Inside the **bandit13** directory, there was this **_ssh private key_**. As stated in the question, there is no password inside this directory but this private file. We have to figure a way to login as **bandit14** using the private key.

<br>

![alt text](image-30.png#center)
I logged out of the ssh server and downloaded the private key from the ssh server to the localhost using the `scp` command.

`scp -P 2220 bandit13@bandit.labs.overthewire.org:/home/bandit13/sshkey.private ~/Desktop`

<br>

![alt text](image-31.png#center)
`man ssh` shows that we can utilize the `-i` option when logging into the ssh server using a private key without password.

<br>

![alt text](image-32.png#center)
Logging into the SSH server failed because the file permissions were too open. **_It is required that your private key files are NOT accessible by others._**

<br>

![alt text](image-33.png#center)
`chmod 700 sshkey.private`

I made the file only accessible by the user using the `chmod 700` command above and succeeded in logging into the server.

<br>

![alt text](image-34.png#center)
As written in the question, the password was located in the following path, **/etc/bandit_pass/bandit14** and it was only accessible by user **bandit14**. Since I logged in as bandit14, I could obtain the password.

<br>

**_password_**: `MU4VWeTyJk8ROof1qqmcBPaLh7lDCPvS`

---

#### Level 14 -> 15

The password for the next level can be retrieved by submitting the password of the current level to port 30000 on localhost.

![alt text](image-35.png#center)

<br>

**_password_**: `8xCjnmgoKbGLhHFAZlGE5Tmu4M2tKJQo`

---

#### Level 15 -> 16

The password for the next level can be retrieved by submitting the password of the current level to port 30001 on localhost using SSL/TLS encryption.

Helpful note: Getting “DONE”, “RENEGOTIATING” or “KEYUPDATE”? Read the “CONNECTED COMMANDS” section in the manpage.

`openssl s_client -connect localhost:30001`

- `openssl`: OpenSSL is widely used for implementing SSL/TLS protocols and managing certificates, keys, and other cryptographic operations.
- `s_client`: This a subcommand within **_opnessl_** that allows you to connect to a remote server over **SSL/TLS**.

When you run the full command, it initiates an SSL/TLS handshake with the server at **localhost** on port **30001**. Once the connection is established, **openssl s_client** will display various details about the connection, including the server's certificate, the cipher being used, and other SSL/TLS parameters.

![alt text](image-36.png#center)

**_password_**: `kSkvUpMQ7lBYyCM4GBPvCvT1BfWRy0Dx`

---

#### Level 16 -> 17

The credentials for the next level can be retrieved by submitting the password of the current level to a port on localhost in the range 31000 to 32000. First find out which of these ports have a server listening on them. Then find out which of those speak SSL/TLS and which don’t. There is only 1 server that will give the next credentials, the others will simply send back to you whatever you send to it.

![alt text](image-37.png#center)
I used **_nmap_** to scan open ports in the range 31000 to 32000 on localhost and found 5 open ports. However, if you look closely under the port **31790**, it writes **Wrong! Please enter the correct current password.** implying a high possibility that it is the port that we are looking for.

<br>

![alt text](image-38.png#center)
`openssl s_client -connect localhost:31790`

I typed the correct password but it kept returning **KEYUPDATE**.

<br>

![alt text](image-43.png#center)
`openssl s_client -connect localhost:31790 -ign_eof`

The `KEYUPDATE` output was returned before because the **_KeyUpdate_** operation is traggered via `K`/`k` and the password included a `K`. You can switch off the interactive commands using the `-ign_eof` option. After correcting the command, I was able to obtain the RSA Private Key.

<br>

![alt text](image-44.png#center)
navigated to the **/tmp** directory because I don't have the permission to create a file in the current working directory. Created a new file called **_ssh.private_** using `vim`.

<br>

![alt text](image-45.png#center)
Transferred the file from the SSH server to my local machine using the `scp` command.

<br>

![alt text](image-46.png#center)
change the file permissions so the user can only have access to the file.

<br>

![alt text](image-47.png#center)
logged into the SSH server using the private key file instead of password.

<br>

![alt text](image-48.png#center)
I know I could use the private key to log in but I grabbed the password to bandit17 that's located in the following path just in case: `/etc/bandit_pass/bandit17`

<br>

**_password_**: `EReVavePLFHtFlFsjn3hyzMlvSuSAcRD`

---
