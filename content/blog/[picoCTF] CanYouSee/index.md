---
title: "[picoCTF] CanYouSee"
author: "Wook Lee"
date: "2024-07-09"
tags: ["picoCTF"]
---

This one from picoCTF was fairly easy, so I was unsure about uploading it. However, there was a part that I wasn't familiar with, and I learned something new. So, why not?

<div style="text-align: center;"><img src="./giphy.gif"></div>

---

### Description

How about some hide and seek?

---

As usual, I started by downloading the provided file to my local machine by using the `wget` command.

```shell
──(kali㉿kali)-[~/Desktop/pico]
└─$ wget https://artifacts.picoctf.net/c_titan/128/unknown.zip
--2024-07-09 16:33:45--  https://artifacts.picoctf.net/c_titan/128/unknown.zip
Resolving artifacts.picoctf.net (artifacts.picoctf.net)... 54.230.253.65, 54.230.253.91, 54.230.253.20, ...
Connecting to artifacts.picoctf.net (artifacts.picoctf.net)|54.230.253.65|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: 2252108 (2.1M) [application/octet-stream]
Saving to: ‘unknown.zip’

unknown.zip                            100%[============================================================================>]   2.15M  11.2MB/s    in 0.2s

2024-07-09 16:33:47 (11.2 MB/s) - ‘unknown.zip’ saved [2252108/2252108]


┌──(kali㉿kali)-[~/Desktop/pico]
└─$ ls
unknown.zip
```

<br>

Unzipped the unknown.zip file.

```shell
┌──(kali㉿kali)-[~/Desktop/pico]
└─$ unzip unknown.zip
Archive:  unknown.zip
  inflating: ukn_reality.jpg

┌──(kali㉿kali)-[~/Desktop/pico]
└─$ ls
ukn_reality.jpg  unknown.zip
```

<br>

Inside the zip file, there was a `.jpg` file named ukn_reality.jpg and it looked like the following:

![alt text](image.png#center)

<br>

Since this was the only file given to me, I knew I had to look up the file's info to capture the flag. So the first thing I tried was to run a `file` command. However, nothing useful was found.

```shell
┌──(kali㉿kali)-[~/Desktop/pico]
└─$ file ukn_reality.jpg
ukn_reality.jpg: JPEG image data, JFIF standard 1.01, resolution (DPI), density 72x72, segment length 16, baseline, precision 8, 4308x2875, components 3
```

<br>

Then I asked chatGPT for ways to view information about an image and it provided some commonly used methods:

1. `file` command
2. `identify` from ImageMagick
3. `exiftool`
4. `exiv2`

I further looked into the commands and thought the `exiftool` would give me the most information about the image.

```shell
┌──(kali㉿kali)-[~/Desktop/pico]
└─$ exiftool ukn_reality.jpg
ExifTool Version Number         : 12.76
File Name                       : ukn_reality.jpg
Directory                       : .
File Size                       : 2.3 MB
File Modification Date/Time     : 2024:03:11 20:05:51-04:00
File Access Date/Time           : 2024:07:09 16:33:57-04:00
File Inode Change Date/Time     : 2024:07:09 16:33:51-04:00
File Permissions                : -rw-r--r--
File Type                       : JPEG
File Type Extension             : jpg
MIME Type                       : image/jpeg
JFIF Version                    : 1.01
Resolution Unit                 : inches
X Resolution                    : 72
Y Resolution                    : 72
XMP Toolkit                     : Image::ExifTool 11.88
Attribution URL                 : cGljb0NURntNRTc0RDQ3QV9ISUREM05fM2I5MjA5YTJ9Cg==
Image Width                     : 4308
Image Height                    : 2875
Encoding Process                : Baseline DCT, Huffman coding
Bits Per Sample                 : 8
Color Components                : 3
Y Cb Cr Sub Sampling            : YCbCr4:2:0 (2 2)
Image Size                      : 4308x2875
Megapixels                      : 12.4
```

<br>

If you look closely the value for `Attribution URL` is not in the expected form. It looks like it's encoded or hashed. I tried decoding the given URL with the `base64` command.

```shell
┌──(kali㉿kali)-[~/Desktop/pico]
└─$ echo cGljb0NURntNRTc0RDQ3QV9ISUREM05fM2I5MjA5YTJ9Cg== | base64 -d
picoCTF{ME74D47A_HIDD3N_3b9209a2}
```

And we got the flag!

**_flag_**: `picoCTF{ME74D47A_HIDD3N_3b9209a2}`
