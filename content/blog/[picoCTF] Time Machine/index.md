---
title: "[picoCTF] Time Machine"
author: "Wook Lee"
date: "2024-07-07"
tags: ["picoCTF"]
---

### Description

What was I last working on? I remember writing a note to help me remember...
You can download the challenge files here:
challenge.zip

---

First, let's download the provided .zip file on our terminal by running the following command:

`wget https://artifacts.picoctf.net/c_titan/160/challenge.zip`

```shell
leewookb-picoctf@webshell:~$ ls
README.txt  challenge.zip
```

Let's unzip the downloaded file by running the following command:

`unzip challenge.zip`

```shell
eewookb-picoctf@webshell:~$ unzip challenge.zip
Archive:  challenge.zip
   creating: drop-in/
  inflating: drop-in/message.txt
   creating: drop-in/.git/
   creating: drop-in/.git/branches/
  inflating: drop-in/.git/description
   creating: drop-in/.git/hooks/
  inflating: drop-in/.git/hooks/applypatch-msg.sample
  inflating: drop-in/.git/hooks/commit-msg.sample
  inflating: drop-in/.git/hooks/fsmonitor-watchman.sample
  inflating: drop-in/.git/hooks/post-update.sample
  inflating: drop-in/.git/hooks/pre-applypatch.sample
  inflating: drop-in/.git/hooks/pre-commit.sample
  inflating: drop-in/.git/hooks/pre-merge-commit.sample
  inflating: drop-in/.git/hooks/pre-push.sample
  inflating: drop-in/.git/hooks/pre-rebase.sample
  inflating: drop-in/.git/hooks/pre-receive.sample
  inflating: drop-in/.git/hooks/prepare-commit-msg.sample
  inflating: drop-in/.git/hooks/update.sample
   creating: drop-in/.git/info/
  inflating: drop-in/.git/info/exclude
   creating: drop-in/.git/refs/
   creating: drop-in/.git/refs/heads/
 extracting: drop-in/.git/refs/heads/master
   creating: drop-in/.git/refs/tags/
 extracting: drop-in/.git/HEAD
  inflating: drop-in/.git/config
   creating: drop-in/.git/objects/
   creating: drop-in/.git/objects/pack/
   creating: drop-in/.git/objects/info/
   creating: drop-in/.git/objects/43/
 extracting: drop-in/.git/objects/43/246218ab4fc7b30e9a9dff073e012316851469
   creating: drop-in/.git/objects/25/
 extracting: drop-in/.git/objects/25/16effb8d70e33bdd0023629b164a77225e1ec2
   creating: drop-in/.git/objects/89/
 extracting: drop-in/.git/objects/89/d296ef533525a1378529be66b22d6a2c01e530
  inflating: drop-in/.git/index
 extracting: drop-in/.git/COMMIT_EDITMSG
   creating: drop-in/.git/logs/
  inflating: drop-in/.git/logs/HEAD
   creating: drop-in/.git/logs/refs/
   creating: drop-in/.git/logs/refs/heads/
  inflating: drop-in/.git/logs/refs/heads/master
leewookb-picoctf@webshell:~$ ls
README.txt  challenge.zip  drop-in
```

<br>

Inside the drop-in folder, we have one file, `message.txt`

```shell
leewookb-picoctf@webshell:~$ cd drop-in
leewookb-picoctf@webshell:~/drop-in$ ls
message.txt
leewookb-picoctf@webshell:~/drop-in$ cat message.txt
This is what I was working on, but I'd need to look at my commit history to know why...
```

<br>

The file didn't include the flag, we were looking for.
Let's run `ls -la` command to reveal all the hidden files.

```shell
leewookb-picoctf@webshell:~/drop-in$ ls -la
total 4
drwxr-xr-x 3 leewookb-picoctf leewookb-picoctf  37 Mar 12 00:07 .
drwxr-xr-x 4 leewookb-picoctf leewookb-picoctf 185 Jul  7 05:04 ..
drwxr-xr-x 8 leewookb-picoctf leewookb-picoctf 166 Mar 12 00:07 .git
-rw-r--r-- 1 leewookb-picoctf leewookb-picoctf  87 Mar 12 00:07 message.txt
leewookb-picoctf@webshell:~/drop-in$ cd .git
leewookb-picoctf@webshell:~/drop-in/.git$ ls
COMMIT_EDITMSG  HEAD  branches  config  description  hooks  index  info  logs  objects  refs
leewookb-picoctf@webshell:~/drop-in/.git$ cat COMMIT_EDITMSG
picoCTF{t1m3m@ch1n3_186cd7d7}
```

<br>

**_flag_**: `picoCTF{t1m3m@ch1n3_186cd7d7}`
