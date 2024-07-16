---
title: "[THM] John The Ripper"
author: "Wook Lee"
date: "2024-07-12"
tags: ["TryHackMe"]
---

#### What are Hashes?

A hash is a way of taking a piece of data any length and representing it in another form that is a fixed length. This masks the original value of the data. This is done by running the original through a hashing algorithm.

#### What makes Hashes secure?

Hashing functions are designed as one-way functions. It's easy to calculate the vash value of a given input; however, it is very difficult to find the original input given the hash value.

#### Wordlists

In order to dictonary attack hashes, you need a list of words that you can hash and compare, this is called a `wordlist`. There are many different wordlists out there, a good collection to use can be found in the `SecLists` repository. On Parrot and Kali, you can find a series of amazing wordlists in the `/usr/share/wordlists` directory.

#### John Basic Syntax

`john [options] [path to file]`
`john`: invokes the John the Ripper program
`[path to file]`: The file containing the hash you're trying to crack, if it's in the same directory you won't need to name a path, just the file.

John has built-in features to detect what type of hash it's being given, and to select appropriate rules and format to crack it for you, however it can be unreliable but if you can't identify what hash type you're working with and just want to try cracking it, use the following syntax:

`john --wordlist=[path to wordlist] [path to file]`

Format-specific cracking
`john --format=[format] --wordlist=[path to wordlist] [path to file]`

Now you know the syntax, modifiers, and methods to crck basic hashes, try it yourself! Download the attached.txt files.

![alt text](image.png#center)

<br>

#### What type of hash is hash1.txt?

```
md5
```

I used `hash-identifier`, a Python tool, to identify the hash type of the hash1.txt.
![alt text](image-1.png#center)

<br>

#### What is the cracked value of hash1.txt?

```
biscuit
```

```shell
┌──(kali㉿kali)-[~/Desktop]
└─$ john --format=raw-md5 --wordlist=/usr/share/wordlists/rockyou.txt first_task_hashes/hash1.txt
Using default input encoding: UTF-8
Loaded 1 password hash (Raw-MD5 [MD5 128/128 ASIMD 4x2])
Warning: no OpenMP support for this hash type, consider --fork=2
Press 'q' or Ctrl-C to abort, almost any other key for status
biscuit          (?)
1g 0:00:00:00 DONE (2024-07-12 12:43) 100.0g/s 409600p/s 409600c/s 409600C/s slimshady..oooooo
Use the "--show --format=Raw-MD5" options to display all of the cracked passwords reliably
Session completed.
```

<br>

#### What type of hash is hash2.txt?

```
sha1
```

![alt text](image-2.png#center)

<br>

#### What is the cracked value of hash2.txt?

```
kangeroo
```

`john --format=raw-sha1 --wordlist=/usr/share/wordlists/rockyou.txt first_task_hashes/hash2.txt`

```shell
┌──(kali㉿kali)-[~/Desktop]
└─$ john --format=raw-sha1 --wordlist=/usr/share/wordlists/rockyou.txt first_task_hashes/hash2.txt
Using default input encoding: UTF-8
Loaded 1 password hash (Raw-SHA1 [SHA1 128/128 ASIMD 4x])
Warning: no OpenMP support for this hash type, consider --fork=2
Press 'q' or Ctrl-C to abort, almost any other key for status
kangeroo         (?)
1g 0:00:00:00 DONE (2024-07-12 12:46) 100.0g/s 11714Kp/s 11714Kc/s 11714KC/s kanon..kalvin1
Use the "--show --format=Raw-SHA1" options to display all of the cracked passwords reliably
Session completed.
```

<br>

#### What type of hash is hash3.txt?

```
sha256
```

```shell
HASH: D7F4D3CCEE7ACD3DD7FAD3AC2BE2AAE9C44F4E9B7FB802D73136D4C53920140A

Possible Hashs:
[+] SHA-256
[+] Haval-256

Least Possible Hashs:
[+] GOST R 34.11-94
[+] RipeMD-256
[+] SNEFRU-256
[+] SHA-256(HMAC)
[+] Haval-256(HMAC)
[+] RipeMD-256(HMAC)
[+] SNEFRU-256(HMAC)
[+] SHA-256(md5($pass))
[+] SHA-256(sha1($pass))
```

<br>

#### What is the cracked value of hash3.txt?

```
microphone
```

`john --format=raw-sha256 --wordlist=/usr/share/wordlists/rockyou.txt first_task_hashes/hash3.txt`

```shell
┌──(kali㉿kali)-[~/Desktop]
└─$ john --format=raw-sha256 --wordlist=/usr/share/wordlists/rockyou.txt first_task_hashes/hash3.txt
Using default input encoding: UTF-8
Loaded 1 password hash (Raw-SHA256 [SHA256 128/128 ASIMD 4x])
Warning: poor OpenMP scalability for this hash type, consider --fork=2
Will run 2 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
microphone       (?)
1g 0:00:00:00 DONE (2024-07-12 12:49) 100.0g/s 8192Kp/s 8192Kc/s 8192KC/s ryanscott..janson
Use the "--show --format=Raw-SHA256" options to display all of the cracked passwords reliably
Session completed.
```

<br>

#### What type of hash is hash4.txt?

```
Whirlpool
```

```shell
HASH: c5a60cc6bbba781c601c5402755ae1044bbf45b78d1183cbf2ca1c865b6c792cf3c6b87791344986c8a832a0f9ca8d0b4afd3d9421a149d57075e1b4e93f90bf

Possible Hashs:
[+] SHA-512
[+] Whirlpool

Least Possible Hashs:
[+] SHA-512(HMAC)
[+] Whirlpool(HMAC)
```

<br>

#### What is the cracked value of hash4.txt?

```
colossal
```

`john --format=whirlpool --wordlist=/usr/share/wordlists/rockyou.txt first_task_hashes/hash4.txt`

```shell
┌──(kali㉿kali)-[~/Desktop]
└─$ john --format=whirlpool --wordlist=/usr/share/wordlists/rockyou.txt first_task_hashes/hash4.txt
Using default input encoding: UTF-8
Loaded 1 password hash (whirlpool [WHIRLPOOL 32/64])
Warning: poor OpenMP scalability for this hash type, consider --fork=2
Will run 2 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
colossal         (?)
1g 0:00:00:00 DONE (2024-07-12 12:51) 6.250g/s 4300Kp/s 4300Kc/s 4300KC/s grass9..blah2007
Use the "--show" option to display all of the cracked passwords reliably
Session completed.
```

<br>

#### Cracking Windows Hashes

Authentication hashes are the hashed versions of passwords that are stored by operating systems. It is sometimes possible to crack them using the brute-force methods that we're using. To get your hands on these hashes, you must often already be a privileged user.

#### NTHash / NTLM (Windows New Technology LAN Manager)

NThash is the hash format that modern Windows Operating System machines will store user and service passwords. It's also commonly referred to as `NTLM` which references the previous version of Windows format for hashing passwords known as `LM`, thus `NT/LM`

You can acquire NTHash/NTLM hashes by dumping the SAM database on a Windows machine, by using a tool like `Mimikatz` or from the Active Directory database: `NTDS.dit`. You may not have to crack the hash to continue privilege escalation as you can often conduct a `pass the hash` attack instead.

![alt text](image-3.png#center)

#### What do we need to set the "format" flag to, in order to crack this?

```
nt
```

<br>

#### What is the cracked value of this password?

```
mushroom
```

```shell
┌──(kali㉿kali)-[~/Desktop]
└─$ john --format=nt --wordlist=/usr/share/wordlists/rockyou.txt ntlm_1605054722641.txt
Using default input encoding: UTF-8
Loaded 1 password hash (NT [MD4 128/128 ASIMD 4x2])
Warning: no OpenMP support for this hash type, consider --fork=2
Press 'q' or Ctrl-C to abort, almost any other key for status
mushroom         (?)
1g 0:00:00:00 DONE (2024-07-12 13:12) 100.0g/s 3276Kp/s 3276Kc/s 3276KC/s 123456..eatme1
Use the "--show --format=NT" options to display all of the cracked passwords reliably
Session completed.
```

<br>

#### Cracking Hashes from `/etc/shadow`

The `/etc/shadow` file is the file on Linux machines where password hashes are stored. It also stores other information, such as the date of last password change and password expiration information. This file is usually only accessible by the root user.

#### Unshadowing

The basinc syntax of unshadow is as follows:

`unshadow [path to passwd] [path to shadow]`

example usage:

`unshadow local_passwd local_shadow > unshadowed.txt`

#### What is the root password?

```
1234
```

![alt text](image-4.png#center)

```shell
┌──(kali㉿kali)-[~/Desktop]
└─$ john --wordlist=/usr/share/wordlists/rockyou.txt unshadowed.txt
Using default input encoding: UTF-8
Loaded 1 password hash (sha512crypt, crypt(3) $6$ [SHA512 128/128 ASIMD 2x])
Cost 1 (iteration count) is 5000 for all loaded hashes
Will run 2 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
1234             (root)
1g 0:00:00:04 DONE (2024-07-13 15:23) 0.2257g/s 1849p/s 1849c/s 1849C/s 123456..whitetiger
Use the "--show" option to display all of the cracked passwords reliably
Session completed.
```

<br>

#### Single Crack Mode

#### What is Joker's password?

```
Jok3r
```

![alt text](image-5.png#center)

<br>

Make sure to modify the content of the file by prepending the username, in this case, `joker`.
![alt text](image-7.png#center)

<br>

![alt text](image-6.png#center)

<br>

#### Custom Rules

You can define your own sets of rules, which John will use to dynmaically create passwords. This is especially useful when you know more information about the password structure of whatever your target is.

custom rules are defined in the `john.conf` file, usually located in `/etc/john/john.conf`

<br>

#### What do custom rules allow us to exploit?

```
Password complexity predictability
```

<br>

#### What rule would we use to add all capital letters to the end of the word?

```
Az"[A-Z]"
```

<br>

#### What flag would we use to call a custom rule called "THMRules"

```
--rule=THMRules
```

<br>

#### Cracking a Password Protected Zip File

We can use John to crack the password on password protected Zip files.

We're going to be using the `zip2john` tool to convert the zip file into a hash format that John can understand and crack. Basic syntax is the following:

`zip2john [options] [zip file] > [output file]`

<br>

#### What is the password for the secure.zip file?

```
pass123
```

`zip2john secure_1605054835063.zip > zip_hash.txt`

`john --wordlist=/usr/share/wordlists/rockyou.txt zip_hash.txt`

```shell
┌──(kali㉿kali)-[~/Desktop]
└─$ zip2john secure_1605054835063.zip > zip_hash.txt
ver 1.0 efh 5455 efh 7875 secure_1605054835063.zip/zippy/flag.txt PKZIP Encr: 2b chk, TS_chk, cmplen=38, decmplen=26, crc=849AB5A6 ts=B689 cs=b689 type=0

┌──(kali㉿kali)-[~/Desktop]
└─$ cat zip_hash.txt
secure_1605054835063.zip/zippy/flag.txt:$pkzip$1*2*2*0*26*1a*849ab5a6*0*48*0*26*b689*964fa5a31f8cefe8e6b3456b578d66a08489def78128450ccf07c28dfa6c197fd148f696e3a2*$/pkzip$:zippy/flag.txt:secure_1605054835063.zip::secure_1605054835063.zip

┌──(kali㉿kali)-[~/Desktop]
└─$ john --wordlist=/usr/share/wordlists/rockyou.txt zip_hash.txt
Using default input encoding: UTF-8
Loaded 1 password hash (PKZIP [32/64])
Will run 2 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
pass123          (secure_1605054835063.zip/zippy/flag.txt)
1g 0:00:00:00 DONE (2024-07-13 16:07) 100.0g/s 3276Kp/s 3276Kc/s 3276KC/s 123456..eatme1
Use the "--show" option to display all of the cracked passwords reliably
Session completed.
```

<br>

#### What is the contents of the flag inside the zip file?

```
THM{w3ll_d0n3_h4sh_r0y4l}
```

![alt text](image-9.png#center)

<br>

#### What is the password for the secure.rar file?

```
password
```

`rar2john secure_1605054844670.rar > rar_hash.txt`

`john --wordlist=/usr/share/wordlists/rockyou.txt rar_hash.txt`

![alt text](image-10.png#center)

<br>

#### What is the contents of the flag inside the zip file?

```
THM{r4r_4rch1ve5_th15_t1m3}
```

`unrar x secure_1605054844670.rar `, **x** stands for "extract with full paths."

![alt text](image-11.png#center)

<br>

#### Cracking SSH Keys with John

Unless configured otherwise, you authenticate your SSH login using a password. However, you can configure key-based authentication, which lets you use your private key, id_rsa, as an authentication key to login to a remote machine over SSH.

<br>

#### SSH2John

`ssh2john [id_rsa private key file] > [output file]`

<br>

#### What is the SSH private key password?

```
mango
```

First, use the `ssh2john` command to process an SSH private key file and converts it into a hash format suitable for password cracking with John the Ripper. The result will be saved to the file `ssh_output.txt`

```shell
┌──(kali㉿kali)-[~/Downloads]
└─$ ssh2john id_rsa_1605800988509.id_rsa > ssh_output.txt
```

<br>

This is what the output looks like after it's processed and converted to a hash format.

```shell
┌──(kali㉿kali)-[~/Downloads]
└─$ cat ssh_output.txt                                                                                   [0/21]
id_rsa_1605800988509.id_rsa:$sshng$1$16$3A98F468854BB3836BF689310D864CE9$1200$08ca19b68bc606b07875701174131b9220d23ef968befc1230eeff0d7c0f904e6734765fe562e8671972e409091f32c80b754ab248976228a5f2c38e8ac63572d7452e75669aeda932275989ce4c077d43287ed227b8f9053e53f2b1c9bb9dfe876378a32e87e7be4e91a845ae8ee4073bf7ac5aad8414253c97cfb73b083107712907da8c704678f46d0b006f7a77b13a04305a988c8e17d83abd2449ed5c3defc8203d7c5f70cef3470b0bbe3fa5a2e957ac55a57ea08b1de4d3fa5436c6160a14b461ac7bc4a3052ddf858de657ecb210989507beb96f7219ac3c3790e89f3af71f7f61ebe23570284a482b1504b067fb1e03ed62201c6db71dab65e5f1577751ddb006fe14ceed4525965fce19f8141373094d1aedbb58cb903f58f6d80695be0382c31e61baaf366d4f2e722316e91ff4dcb3df15702008b5be3c0b2a81b3f452ef3257c425dd26119324b4de3652e90b91afd87ca2bc41c70abd0d97557d4037952b63c0a0d7c7ab6ed538c3d76bdf488683213e8d8e897ab51c4990b137d04e5044ccbf8cadbdce9eeec5e50f3d487b1f21e86a2b2785caedbf9503d2d8585b2138d82b35e70d1da03c9c574962cdb6e4d2de761a594ab8c082d88b43a027649012feb28b6a022c0ab49cf05e8b91e36bda935f188c1bb05925da2168dd15af917ba20a8532010892853da5cb1a8ff80cc5d3aa1dd3fe66543bf14d9b44d082261fd61976718bb5eea1d911ddb7fb0cc0505b39cec36ef7bd8e8d9d826eda5f7e1a5a51067ead2f78cf69f85de97be5a8f371174356788554b6bf134072b93bf6728ec26fe19c2485be9e7428208a66cc1e79329ac16f3034605c63550a424ed8cac39f965b6ffe83240c6709607eaef99b189100ef33e000b4195e07ec5c67bdaf2ca1acbd08327f0c4dcfae322883f7be964cb22393541e883c8c5b748237a900aab709b6286cea66a214a9fe4e3a1203f999fd995aa049767355e2658828c4a82d58ca15343f0abe6b2779e880ed2682b4730103a84a3410e6c822098d82b04d665b8bf98bc3b69cae0c8d8c9d140dc99056279d5f330bc439bfdceaf38a56fd1362ce78e96deb49a9f6756ec9b64eeba8f4725ec056ab206e37823d052d539d38016abf792858a169cbbe0f6f0d0049c6d49228833aa8ec10ede0c183ac737e54346949485e5ffc1bc3105e5686c8b1f6fb8cdb14949aa97b833757d02b970e96cb1281c472a5cb26cfa7cfda0be5bd45cf14d4bc28ccd2be4dd09c6a2ce0cf668035d2aa39a8345ea154543491436bf8f5e605d86e266d40227f48684e696a225877624ddddf0afe05d0aeec29ad28edb0f8cda0f341ddbbd454bd3c238d1c499effa6bf6f796dae0983182f36fae4781552cb8d9426fc57132c27a735c5365a5d355a4c4f21d5d7ed2ea11bb2ed856156390673545418f47359fd1092767f6dfb3321ee14a0043352fdbaa5cb0e75fde2ec5909c0533251f30bd56ad7e34a8a31b009b53c64e9f2de9fd57a0f561564e6a961158cc0b54fcfc43046d9641788ac5e25b89bdb7890c4e6532d1bfabd4d49ae7d3740506c4ecb6bc5cb6a12bc24ed2e0f913338c7dfa08ada66a6128e7de56794d1d2170d6324af0cd72bc8abcff177f0942d9df5d99d48c8f946fd856d9ccb424966835aa06c94996abcc169aef6f246bbbd7489ec026a
```

<br>

Finally, cracking the hash with John the Ripper using the `rockyou` wordlist.

```shell
┌──(kali㉿kali)-[~/Downloads]
└─$ john --wordlist=/usr/share/wordlists/rockyou.txt ssh_output.txt
Using default input encoding: UTF-8
Loaded 1 password hash (SSH, SSH private key [RSA/DSA/EC/OPENSSH 32/64])
Cost 1 (KDF/cipher [0=MD5/AES 1=MD5/3DES 2=Bcrypt/AES]) is 0 for all loaded hashes
Cost 2 (iteration count) is 1 for all loaded hashes
Will run 2 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
mango            (id_rsa_1605800988509.id_rsa)
1g 0:00:00:00 DONE (2024-07-13 16:30) 100.0g/s 430400p/s 430400c/s 430400C/s mango..Nicole
Use the "--show" option to display all of the cracked passwords reliably
Session completed.
```

<br>
