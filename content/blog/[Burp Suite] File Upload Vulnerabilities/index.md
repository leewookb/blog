---
title: "[Burp Suite] File Upload Vuln."
author: "Wook Lee"
date: "2024-10-19"
tags: ["Burp Suite"]
---


A major event happened in my life over the past 2 months, which is why I haven't been able to keep up with posting write-ups on my blog. But just because I haven't written anything for a while doesn't necessarily mean that I haven't been studying. On a positive note, I got a job as a Cybersecurity specialist! While my current role doesn't involve pentesting, which is a bit of bummer, It's still an amazing opportunity to expand my skills and expertise in this field that I love. I already have learned a ton and I am excited to keep growing and hopefully get involved in pentesting in the near future.

---

### Lab: Remote code execution via web shell upload

This lab contains a vulnerable image upload function. It doesn't perform any validation on the files users upload before storing them on the server's file system.

To solve the lab, upload a basic PHP web shell and use it to exfiltrate the content of the file `/home/carlos/secret`. Submit this secret using the button provided in the lab banner.

You can log in to your own account using the following credentials: `wiener:peter`

---

![alt text](image.png#center)

This is the main page of the Lab

<br>

![alt text](image-1.png#center)
If we click on `view post`, we can see we have a functionality to upload files at the bottom of the page

<br>


![alt text](image-2.png#center)
Or if we click on `My account` and login with the provided credentials `wiener:peter`, upload functionality is also available there.

<br>


![alt text](image-8.png#center)
First, I chose a cat image file that was available from my Desktop and toggled `Intercept on` button on Burp Suite to intercept the traffic. Then, uploaded the file.

<br>


![alt text](image-9.png#center)
This is the content of the POST request we just made to upload the cat image file. If you look closely, it specifies the `name`, `filename`, `Content-Type` of the uploaded file and also the random texts below are actually what make up the image file.

<br>


![alt text](image-10.png#center)
We get this confirmation message saying that our file is uploaded to the following path `avatars/{filename}`

<br>


![alt text](image-11.png#center)
After clicking on `Back to My Account`, we can see that the uploaded cat image is set as  our avatar image.

<br>

![alt text](image-12.png#center)
I sent the POST request I made earlier to Repeater and changed the `filename` to **exploit.php** and deleted the texts below and replaced them with the php webshell code.
```php
<?php echo file_get_contents('/etc/passwd'); ?>
```

<br>

![alt text](image-13.png#center)
This is the GET request to retrieve the previously uploded image file. I replaced the image file name with `exploit.php` because it's the name of the webshell file we just replaced the image file with.

<br>

![alt text](image-14.png#center)
If you look on the right side of the screenshot, we successfully retrieved `/etc/passwd`

<br>

![alt text](image-15.png#center)
Now, all we have to do is replace `/etc/passwd` with `/home/carlos/secret` because that's the path where the answer to the question is located. It's specified in the question.

<br>

![alt text](image-16.png#center)
The server responded with the secret `E7GnNlW2W2SAasyDLDzzHeCsKYNJ8nNk`

<br>


![alt text](image-17.png#center)
I submitted the returned string as the answer

<br>

![alt text](image-18.png#center)