---
title: "[THM] OWASP Juice Shop"
author: "Wook Lee"
date: "2024-07-03"
tags: ["TryHackMe"]
---

We will look at OWASP's TOP 10 vulnerabilities in web applications. But for this, we will be looking at OWASP's own creation, Juice Shop.

![alt text](image.png#center)

<br>

#### Question #1: What's the Administrator's email address?

```
admin@juice-sh.op
```

![alt text](image-1.png#center)

<br>

#### Question #2: What parameter is used for searching?

```
q
```

![alt text](image-2.png#center)

When I typed `asdfsdf` on the search bar, I noticed that the URL changed to `http://10.10.113.37/#/search?q=asdfsdf`. we can see that the parameter is `q`.

<br>

#### Question #3: What show does Jim reference in his review?

```
Star Trek
```

![alt text](image-3.png#center)

Jim did a review on the Green Smoothie product. We can see that he mentions a replicator. Replicator is a reference to a TV show called Star Trek.

<br>

#### Question #4 Log into the administrator account!

![alt text](image-4.png#center)
On the login page, just type any credentials for Burpsuite to capture the request

```shell
POST /rest/user/login HTTP/1.1
Host: 10.10.113.37
User-Agent: Mozilla/5.0 (X11; Linux aarch64; rv:109.0) Gecko/20100101 Firefox/115.0
Accept: application/json, text/plain, */*
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate, br
Content-Type: application/json
Content-Length: 44
Origin: http://10.10.113.37
Connection: keep-alive
Referer: http://10.10.113.37/
Cookie: io=QdUYP1oidC2VzPTOAAAA; language=en; cookieconsent_status=dismiss

{"email":"test@gmail.com","password":"test"}
```

As you could see our request has been captured. Next, we are going to change the email value, `test@gmail.com` to `' or 1=1`. `{"email":"' or 1=1","password":"test"}`

Here's how this works:

1.  The character `'` will close the brackets in the SQL query
2.  `OR` in a SQL statement will return true if either side of it is true. As 1=1 is always true, the whole statement becomes true. Thus it will tell the server that the email is valid, and log us into user id 0, which happens to be the administrator account.
3.  The `--` character is used in SQL to comment out data, any restrictions on the login will no longer work as they are interpreted as a comment.

![alt text](image-5.png#center)
Send a request with the SQL injection code then you will be logged in as admin

<br>

#### Question 5: Log into the Bender account!

```
fb364762a3c102b2db932069c0e6b78e738d4066
```

![alt text](image-6.png#center)
![alt text](image-7.png#center)

<br>

#### Question 6: Bruteforce the Administrator account's password

We have used SQL injection to log into the Administrator account but still do not know the password.
Once again capture a login request but this time send it to Intruder on Burpsuite.

Go to Positions and then select the `Clear § `. In the password field, place two `§` inside the quotes.
The `§§` is not two separate inputs but Burp's implementation of quotations.
![alt text](image-8.png#center)
<br>

For the payload, we will be using the `best1050.txt from Seclists` for Bruteforce attack.
Seclists can be downloaded via `sudo apt-get install seclists`. Also, the list is located in `/usr/share/wordlists/SecLists/Passwords/Common-Credentials/best1050.txt`
<br>

![alt text](image-9.png#center)

![alt text](image-10.png#center)
As we can see the status code is 200 for the payload, `admin123`
<br>

![alt text](image-11.png#center)

<br>

#### Question 7: Reset Jim's Password

When typed into the email field in the Forgot Password page, Jim's security question is set to `Your eldest siblings middle name?`
However, we already saw that Jim used a Star Trek reference with his review on a juice.

![alt text](image-12.png#center)

<br>

![alt text](image-13.png#center#center)

when googled "Jim Star Trek", the fictional character's name is actually `James T. Kirk` and its eldest siblings middle name is `Samuel`
![alt text](image-14.png#center#center)
![alt text](image-15.png#center)

<br>

#### Question 8: Access the Confidential Document!

![alt text](image-16.png#center)

on the bottom left, we can see that it links to `http://10.10.187.53/ft/legal.md`. Navigating to that `/ftp/` directory reveals that it is exposed to the public.

![alt text](image-17.png#center)

Some of the files appear to be confidential. Download the files and go back to the homepage to receive the flag.

![alt text](image-18.png#center)
`edf9281222395a1c5fee9b89e32175f1ccf50c5b`

<br>

#### Question 9: Log into MC SafeSearch's account

Watch the following video to get the password for MC Safe Search's account:
<a href="https://www.youtube.com/watch?v=v59CX2DiX0Y">Link</a>

In his song, he mentions that his password is "Mr. Noodles" but he replaced some vowels into zeros. So, the password to the *mc.safesearch@juice-sh.op* is "**Mr. N00dles**.

`66bdcffad9e698fd534003fbb3cc7e2b7b55d7f0`

<br>

#### Question 10: Download the Backup file!

On http://10.10.176.53/ftp, when we try to download `package.json.bak` file, we encounter a 403 error saying that only .md and .pdf files can be downloaded.

To get around this, we can use a character bypass called `Poison Null Byte`.
A Poison Null Byte looks like this `$00`

But because we are downloading it using the url, we will need to encode this into a url encoded format, which is `%2500`.

The number "2500" in the context of a "Poison Null Byte" is a result of URL encoding

A Poison Null Byte is actually a `NULL terminator`. By placing a NULL character in the string at a certain byte, the string will tell the server to terminate at that point, nulling the rest of the string.

The final url should look like this: `10.10.176.53/ftp/package.json.bak%2500.md`

<br>

#### Question 11: Access the administration page

Modern-day systems will allow for multiple users to have access to different pages. Administrators most commonly use an admin page to edit, add, and remove different elements of a website.

When `Broken Access Control` exploits or bugs are found, it will be categorized into one of two types:

- Horizontal Privilege Escalation
- Vertical Privilege Escalation

Look for a javascript file for `main-es2015.js` in Debugger on FireFox (or Sources on Chrome)

![alt text](image-19.png#center)

This shows us that there's a path towards `/#/administration`, but going there while not logged in doesn't work. As this is an Administrator page, it makes sens that we need to be in the Admin account in order to view it.

<br>

![alt text](image-20.png#center)
This image above is what we see when we logged in as admin and navigated to `/#/administration` page.

`946a799363226a24822008503f5d1324536629a0`

<br>

#### Question 12: View another user's shopping basket.

Log into the Admin account and click on 'Your Basket'. Make sure Burp is running so you can capture the request.

`GET /rest/basket/1 HTTP/1.1`

Change the number 1 after `/basket/` to 2

![alt text](image-21.png#center)

It will show you the basket of User ID 2. You can do this for other User IDs as well

<br>

#### Question 13: Remove all 5-star reviews.

Navigate back to `/#/administration` page and remove all the five-star review by clicking the bin icon.

![alt text](image-22.png#center)

`50c97bcce0b895e446d61c83a21df371ac2266ef`

<br>

#### Question 14: Perform a DOM XSS!

XSS or Cross-site scripting is a vulnerability that allows attackers to run javascript in web applications. Their complexity ranges from easy to extremely hard, as each web application parses the queries in a different way.

There are three major types of XSS attacks:

1. DOM XSS
2. Persistent XSS
3. Reflected XSS

We will be using the iframe element with a javascript alert tag:

`<iframe src="javascript:alert('xss')">`

It is common practice that the search bar will send a request to the server in which it will then send back the related information, but this is where the flaw lies. Without correct input sanitastion, we are able to perform an XSS attack against the search bar.

`9aaf4bbea5c30d00a1f5bbcfce4db6d4b0efe0bf`

<br>

#### Question 15: Perform a persistent XSS!

First, log in to the `admin` account. We are going to navigate to the `Last Login IP` page for this attack.

The Last Login IP should say `0.0.0.0`
As it logs the 'last' login IP, we will now logout so that it logs the 'new' IP. Make sure that Burp intercept is on, so it will catch the logout request.

![alt text](image-23.png#center)

After we catch the logout request, we will add a new header like the image above and forward the request to the server with the newly added header.

Then when signing back into the admin account and navigating to the Last Login IP page again, we will see the XSS alert.

![alt text](image-24.png#center)

`149aa8ce13d7a4a8a931472308e269c94dc5f156`

<br>

#### Question 16: Perform a reflected XSS!

Login into the admin account and navigate to the 'Order History' page.
From there you will see a 'Truck' icon, clicking on that will bring you to the track result page. You will also see that there is an id paird with the order.

`http://10.10.129.167/#/track-result?id=5267-b3f50aba1f1b9125`

We will use the iframe XSS, `<iframe src="javascript:alert('xss')"`>, in the place of the
`5267-b3f50aba1f1b9125`

After submitting the URL, refresh the page and you will get an alert saying XSS.

![alt text](image-25.png#center)

`23cefee1527bde039295b2616eeb29e1edc660a0`

<br>
