---
title: "[picoCTF] WebDecode"
author: "Wook Lee"
date: "2024-07-06"
tags: ["picoCTF"]
---

### Description

Do you know how to use the web inspector?
Start searching <a href="http://titan.picoctf.net:64333/" target="_blank">here</a> to find the flag

---

If you click on the link provided, it takes you to a website.
![alt text](image.png#center)

There's a navbar at the top with three options, `home`, `about`, and `contact`
If you navigate to the `about` page, it says **Try inspecting the page!! You might find it there**.

<br>

![alt text](image-1.png#center)
When you inspect the elements of the page, you'll notice that the section tag has an attribute called `notify_true="cGljb0NURnt3ZWJfc3VjYzNzc2Z1bGx5X2QzYzBkZWRfMjgzZTYyZmV9"`. This looks suspicious and encrypted.

<br>

I googled the word `decode` and the first thing that popped up was base64. I decided to give it a try since we didn't know if the string was actually encoded or, if it was, how it was encoded

![alt text](image-3.png#center)

<br>

I decoded the found string and it turned out to be an encrypted string.

![alt text](image-2.png#center)

we found the flag.

**_flag_**: picoCTF{web_succ3ssfully_d3c0ded_283e62fe}
