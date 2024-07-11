---
title: "[picoCTF] Secret of the Polyglot"
author: "Wook Lee"
date: "2024-07-07"
tags: ["picoCTF"]
---

### Description

The Network Operations Center (NOC) of your local institution picked up a suspicious file, they're getting conflicting information on what type of file it is. They've brought you in as an external expert to examine the file. Can you extract all the information from this strange file?
Download the suspicious file.

---

First, I downloaded the provided file on my Kali Linux VM by the follwing command:
`wget https://artifacts.picoctf.net/c_titan/9/flag2of2-final.pdf`

The name of the file is `flag2of2-final.pdf`

I opened the file and it didn't have much information except some random strings, `1n_pn9_&_pdf_7f9bccd1}`

![alt text](image.png#center)

<br>

I tried opening the file using the `nano` command in the terminal, hoping to see more information. When opened with nano, the top of the file stated `PNG` and included some non-human readable text. This made me suspect that the file is not a `PDF` but a `PNG` file.

![alt text](image-1.png#center)

<br>

The suspicion turned out to be true. The `file` command is used to determine the type of a file and the file was indeed a .png file.

```shell
┌──(kali㉿kali)-[~/Desktop]
└─$ file flag2of2-final.pdf
flag2of2-final.pdf: PNG image data, 50 x 50, 8-bit/color RGBA, non-interlaced
```

<br>

So I created a new file by running the following command:
`cp flag2of2-final.pdf flag2.png`

And check out the newly created file! We're really close to the capturing the flag.
![alt text](image-2.png#center)

We're missing the rest of the file but notice the name of the original file, `flag2of2-final.pdf`. The name implies that it is the second part of the flag.

**_flag_**: `picoCTF{f1u3n7*1n_pn9*&\_pdf_7f9bccd1}`
