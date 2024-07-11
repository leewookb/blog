---
title: "[picoCTF] packer"
author: "Wook Lee"
date: "2024-07-08"
tags: ["picoCTF"]
---

### Description

Reverse this linux executable?

---

First, I downloaded the file to my local machine using the **wget** comand.

`wget https://artifacts.picoctf.net/c_titan/103/out`

It seems like a binary file that contains non-human readable contents inside it

```shell
┌──(kali㉿kali)-[~/Desktop]
└─$ ls
hello.php5  hello.png  leewookb.ovpn  out  shell.js

┌──(kali㉿kali)-[~/Desktop]
└─$ file out
out: ELF 64-bit LSB executable, x86-64, version 1 (GNU/Linux), statically linked, no section header
```

![alt text](image.png#center)

<br>

I used the `strings` command to extract and print only the readable strings from the binary file and found these words that might be useful.

![alt text](image-1.png#center)

It clearly mentions that the file is packed with the UPX executable. On the internet, I found the command to decompress the executable.

`upx -d {your executable}`

```shell
┌──(kali㉿kali)-[~/Desktop]
└─$ upx -d out -o original
                       Ultimate Packer for eXecutables
                          Copyright (C) 1996 - 2024
UPX 4.2.2       Markus Oberhumer, Laszlo Molnar & John Reiser    Jan 3rd 2024

        File size         Ratio      Format      Name
   --------------------   ------   -----------   -----------
[WARNING] bad b_info at 0x4b710

[WARNING] ... recovery at 0x4b70c

    877724 <-    336512   38.34%   linux/amd64   original

Unpacked 1 file.
```

the `-o` option followed by `original` specifies that the unpacked file should be saved as `original`.

<br>

Now if you run the `strings original` command, the content is much more clear like the following:

![alt text](image-2.png#center)

<br>

However, the content is too large. I can't read everything line by line. Let's try if the content includes any flag by running the **grep** command.

`strings original | grep "flag"`

```shell
┌──(kali㉿kali)-[~/Desktop]
└─$ strings original | grep "flag"
Password correct, please see flag: 7069636f4354467b5539585f556e5034636b314e365f42316e34526933535f36666639363465667d
(mode_flags & PRINTF_FORTIFY) != 0
WARNING: Unsupported flag value(s) of 0x%x in DT_FLAGS_1.
version == NULL || !(flags & DL_LOOKUP_RETURN_NEWEST)
flag.c
_dl_x86_hwcap_flags
_dl_stack_flags
```

Notice that the second line displays some hex dump values. Let's try to convert that hex dump back to its original format.

`xxd -r -p <<< "7069636f4354467b5539585f556e5034636b314e365f42316e34526933535f36666639363465667d"`

Let's break down the command:

- `xxd`: This is a command-line tool that creates a hex dump or converts a hex dump back to its binary form.
- `-r`: This option tells `xxd` to reverse the operation, meaning it will convert a hex dump back into its original binary form.
- `-p`: This option tells `xxd` to use a plain (continuous) hex dump format, without any additional formatting (like line numbers or spaces).
- `<<<`: This is known as a **here-string** in bash. When you use the here-string, bash takes the string inside the quotes on the right and provides it as input to the command on its left.

output:

```shell
┌──(kali㉿kali)-[~/Desktop]
└─$ xxd -r -p <<< "7069636f4354467b5539585f556e5034636b314e365f42316e34526933535f36666639363465667d"
picoCTF{U9X_UnP4ck1N6_B1n4Ri3S_6ff964ef}
```

**_flag_**: `picoCTF{U9X_UnP4ck1N6_B1n4Ri3S_6ff964ef}`
