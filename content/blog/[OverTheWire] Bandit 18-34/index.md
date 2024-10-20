---
title: "[OverTheWire] Bandit 18-34"
author: "Wook Lee"
date: "2024-08-17"
tags: ["Over The Wire", "Bandit"]
---

#### Level 17 -> 18

There are 2 files in the homedirectory: passwords.old and passwords.new. The password for the next level is in passwords.new and is the only line that has been changed between passwords.old and passwords.new

![alt text](image.png#center)

As stated in the question, my job was to find the line that had been changed between **passwords.old** file and **passwords.new** file. We can utilize the `diff` command to solve this question.

the output `42c42` indicates that there is a change on line 42 of both files.

- The first `42` refers to line 42 in the **passwords.new** file.
- The second `42` refers to line 42 in the **passwords.old** file.
- The `c` between them stands for "change".

`< x2gLTTjFwMOhQ8oWNbMN362QKxfRqGlO` This line shows the content of line 42 in the **passwords.new** file.

`> bSrACvJvvBSxEM2SGsV5sn09vc3xgqyp` This line shows the content of line 42 in the **passwords.old** file.

**_password_**: `x2gLTTjFwMOhQ8oWNbMN362QKxfRqGlO`

---

#### Level 18 -> 19

The password for the next level is stored in a file readme in the homedirectory. Unfortunately, someone has modified .bashrc to log you out when you log in with SSH.

![alt text](image-1.png#center)
As stated in the question, we are not able to login to the bash shell becausome someone has modified the **.bashrc** file.

<br>

![alt text](image-2.png#center)
I learned that under the **/etc/shells** directory, the details of all the shells that are available on a system is stored there. These are the shells I can use on my local system.

<br>

![alt text](image-3.png#center)
Also the man page of **ssh** command explains that `-t` option is used to specify the shell to be used to login to the system.

<br>

![alt text](image-5.png#center)
Combined everything, the following command allowed me to login to the server as **bandit18** and found the password for the next level.

`ssh bandit18@bandit.labs.overthewire.org -p 2220 -t "/bin/sh"`

<br>

**_password_**: `cGWpMaKXVwDUNgPAVJbWYuGHVn9zl3j8`

---

#### Level 19 -> 20

To gain access to the next level, you should use the setuid binary in the homedirectory. Execute it without arguments to find out how to use it. The password for this level can be found in the usual place (/etc/bandit_pass), after you have used the setuid binary.

![alt text](image-6.png#center)
confirmed the setuid binary existed inside the directory

<br>

![alt text](image-7.png#center)
executed the file without any arugument to find out how to use it. Following the example, I noticed that the username associated with the euid is **bandit20** while all other IDs are associated with the username **bandit19**. The `euid` or `effective user ID` is used for **_privilege checks during execution_** meaning we can run commands as if we were the bandit20 user.

<br>

![alt text](image-8.png#center)
looked up the password of bandit20 user with the SUID binary.

**_password_**: `0qXahG8ZjOVMN9Ghs7iOWsCfZyXOUbYO`

---

#### Level 20 -> 21

There is a setuid binary in the homedirectory that does the following: it makes a connection to localhost on the port you specify as a commandline argument. It then reads a line of text from the connection and compares it to the password in the previous level (bandit20). If the password is correct, it will transmit the password for the next level (bandit21).

NOTE: Try connecting to your own network daemon to see if it works as you think

![alt text](image-9.png#center)
For this level, I found using `tmux` very useful because it allows you to create multiple panes on one window, making it easy to swtich between them. The question hints that we have to make a connection to localhost so I set up a listener on the local as you can see in the right pane.

<br>

![alt text](image-10.png#center)
Then I ran the SUID binary by running the command with the port number 1234 as the argument `./suconnect 1234` and the connection was successfully made.

<br>

![alt text](image-11.png#center)
It prompted me to input the password, which I did. The message "Password matches, sending next password" was returned in the left pane and the password for the next level appeared in the right pane.

<br>

**_password_**: `EeoULMCra2q0dSkYj561DX7s1CpBuOBt`

---

#### Level 21 -> 22

A program is running automatically at regular intervals from cron, the time-based job scheduler. Look in /etc/cron.d/ for the configuration and see what command is being executed.

![alt text](image-12.png#center)
Inside the **/etc/cron.d** directory, there were multiple files but the file that we are interested in is **_cronjob_bandit22_**.

<br>

![alt text](image-13.png#center)
The **_cronjob_bandit22_** file is scheduled to run at two intervals.

1. `@reboot`: runs the specified command when the system reboots.
2. `* * * * *`: Runs the specified command every minute.

The scheduled command is that the user **bandit22** is running the **_cronjob_bandit22.sh_** file and any output is redirected to **/dev/null**, which means it is discarded.

I looked inside the **_.sh_** file and found that it stores the password for bandit22 in the following path: **_/tmp/t7O6lds9S0RqQh9aMcz6ShpAoZKF7fgv_**.

<br>

![alt text](image-14.png#center)
All that's left for us to do is look inside that file to retrieve the bandit22's password.

**_password_**: `tRae0UfB9v0UzbCdn9cY0gQnds9GF58Q`

---

#### Level 22 -> 23

A program is running automatically at regular intervals from cron, the time-based job scheduler. Look in /etc/cron.d/ for the configuration and see what command is being executed.

![alt text](image-15.png#center)
This time we need to examine the **_cronjob_bandit23_** file inside the **_/etc/cron.d_** directory. Just like the previous question, the cronjob is scheduled to execute the following shell script: **/usr/bin/cronjob_bandit23.sh**.

<br>

![alt text](image-16.png#center)
Let's break down the shell script.

- `md5sum`: calculates the MD5 hash of the input string.
- `cut -d ' ' -f 1`: The **cut** command is used to split the input into sections based on a delimiter.
  - `-d ' '`: specifies that the delimiter is a space character.
  - `-f 1`: specifies that you want to select the first field.

The entire command generates the MD5 hash of the string `I am user <CURRENT USER>` and then extracts and displays only the hash value and then stores the current user's password inside the following location: **/tmp/<MD5_HASH>**

<br>

![alt text](image-17.png#center)
So all I had to do was replace the `$myname` variable with `bandit23` and run the command because I am currently looking for bandit23's password. It will return the md5 hash which is actually going to be the name of the file which stores bandit23's password.

**_password_**: `0Zf11ioIjMVN551jX3CmStKLYqjk54Ga`

---

#### Level 23 -> 24

A program is running automatically at regular intervals from cron, the time-based job scheduler. Look in /etc/cron.d/ for the configuration and see what command is being executed.

![alt text](image-18.png#center)
you can see that we can execute the shell file as the **_bandit24_** user

<br>

![alt text](image-19.png#center)
The code does exactly what the echo command says: **_Execute and delete all scripts in /var/spool/$myname/foo_**. In this case, the `myname` variable refers to **bandit24**.

<br>

![alt text](image-20.png#center)
confirmed that anyone can create files inside the `/var/spool/bandit24/foo` directory.

<br>

![alt text](image-23.png#center)
I created a **tmp directory** using the command `mktemp -d`, which automatically generated a directory named `tmp.ajuCmrTuOu`. Inside this directory, I created a file containing the code to retrieve the password for bandit24 and stored it in a file named **password** within the tmp directory.

<br>

![alt text](image-24.png#center)
Made sure the tmp directory and files are accessible by giving them 777 permissions.

<br>

![alt text](image-25.png#center)
I copied the file containing the code to retrieve the password to the path `/var/spool/bandit24/foo/`. This is the directory where all scripts will be executed and deleted. After this, you can wait for a minute and then read the password file to get the password for bandit24, as this is a cronjob that runs every minute.

<br>

**_password_**: `gb8KRRCsshuZXI0tUuR6ypOFjiZbf3G8`

---

#### Level 24 -> 25

A daemon is listening on port 30002 and will give you the password for bandit25 if given the password for bandit24 and a secret numeric 4-digit pincode. There is no way to retrieve the pincode except by going through all of the 10000 combinations, called brute-forcing.
You do not need to create new connections each time

![alt text](image-28.png#center)
When you connect to port 30002, you will be prompted to input **_bandit24's password_** followed by a secret numeric **_4-digit pincode_** seaparated by a space. If either is incorrect, you will receive an error message and be prompted to try again.

<br>

![alt text](image-30.png#center)
Again, I made a tmp directory by running the `mktemp -d` command and wrote a bash script that iterates from 0000 to 9999 prepending each value with the bandit24 password. Each combination is stored in a file named **bruteforce.txt**.

Once all possibilites are written, the script connects to the port and inputs each entry of the bruteforce, logging the output of each attempt into a **result.txt** file.

<br>

![alt text](image-31.png#center)
Before running the bash script, I gave it the **executable** permission.

<br>

![alt text](image-32.png#center)
This is the content of the **bruteforce.txt** file after running the bash script. Notice the scroll bar on the right is very small because of 10,000 possible entries.

<br>

![alt text](image-33.png#center)
This is the content of the **result.txt** file after running the bash script completion. Notice it returned **Wrong! ...** for every entry with an incorrect pincode but the last entry returned **Correct!** along with the password for bandit25.

<br>

![alt text](image-34.png#center)
Another way to find the password for bandit25 is by using the `grep` command. The `-v` flag inverts the match, meaning it will select lines that do **_NOT_** match the pattern. We saw that for every entry that had the either wrong password or pincode returned the string "Wrong", we can leverage this to find any line that does not include the pattern by using the option `-v`.

**_password_**: `iCi86ttT4KSNe1armKiwbQNmB3YJP3q4`

---

#### Level 25 -> 26

Logging in to bandit26 from bandit25 should be fairly easy… The shell for user bandit26 is not /bin/bash, but something else. Find out what it is, how it works and how to break out of it.

![alt text](image-35.png#center)
When logged in as bandit25, we have the **bandit26.sshkey** inside the home directory.

<br>

![alt text](image-36.png#center)
Transferred the keyfile to my local environment using the **_scp_** command.

<br>

![alt text](image-37.png#center)
In order to login with the key file, you have to change the file permissions so that only the user has permissions to read/write/execute the file.

<br>

![alt text](image-38.png#center)
However, the server automatically kicked me out.

<br>

![alt text](image-39.png#center)
We have to remember that each user in SSH has a default shell. The **_/etc/passwd_** file displays what shell is the default for a user. As you can see, bandit26 user has **/bin/showtext** shell as the default.

<br>

![alt text](image-40.png#center)
We can see that the script opens a file called **_text.txt_** with the **_more_** program.

<br>

![alt text](image-41.png#center)
In order to make the **more** program run, we need to make the window smaller, then try logging in.

<br>

![alt text](image-42.png#center)
As you can see when we made the terminal window smaller, the **more** program will go into command mode.

<br>

![alt text](image-43.png#center)
From there, we can then use `v` to go into vim and rescale the window back.

<br>

![alt text](image-44.png#center)
To use the shell on vim, type `set shell=/bin/bash` and then use `:shell`. After getting the shell, I navigated to the `/etc/bandit_pass/bandit26` file to get the password for bandit26.

<br>

**_password_**: `s0773xxkk0MXfdqOfPRVr9L3jJBUOgCZ`

---

#### Level 26 -> 27

Good job getting a shell! Now hurry and grab the password for bandit27!

![alt text](image-45.png#center)

<br>

![alt text](image-46.png#center)

<br>

**_password_**: `upsNCc7vzaRDx6oZC6GiR6ERwe1MowGB`

---

#### Level 27 -> 28

There is a git repository at ssh://bandit27-git@localhost/home/bandit27-git/repo via the port 2220. The password for the user bandit27-git is the same as for the user bandit27.

Clone the repository and find the password for the next level.

![alt text](image-47.png#center)
I had to make a temporary directory inside the `/tmp` directory because the I did not have the permission to write in the `home` directory.

<br>

![alt text](image-48.png#center)
Failed to connect to the ssh server because I forgot to specify the port number 2220.

<br>

![alt text](image-49.png#center)
updated my command as the following:
`git clone ssh://bandit27-git@localhost:2220/home/bandit27-git/repo`

<br>

![alt text](image-50.png#center)
Successfully cloned the git repo and there was `README` file, which contained the password for the next level, inside the `repo` directory.

<br>

**_password_**: `Yz9IpL0sBcCeuG7m9uQFt8ZNpS4HZRcN`

---

#### Level 28 -> 29

There is a git repository at ssh://bandit28-git@localhost/home/bandit28-git/repo via the port 2220. The password for the user bandit28-git is the same as for the user bandit28.

Clone the repository and find the password for the next level.

![alt text](image-51.png#center)
Since the question is exactly the same as the previous question except the bandit user#, I repeated the same process by making a temporary directory and cloned the repo.

<br>

![alt text](image-52.png#center)
Interestingly, the password inside `README.md` was censored.

<br>

![alt text](image-53.png#center)
As expected, the censored password, `xxxxxxx` did not work.

<br>

![alt text](image-55.png#center)
Since we know this question is related to `git`, I tried to look for submissions to the repo by typing `git log` and it showed three commits. The comment `fix info leak` maybe refers to censoring the leaked password.

<br>

![alt text](image-56.png#center)
I used the `git checkout` command to check the previous commit to verify if my hypothesis was correct.

<br>

![alt text](image-57.png#center)
Now we are on the previous version of the repo, I used the `cat` command to check out the readme file.

And! There was the uncensored password for bandit29.

<br>

**_password_**: `4pT1t5DENaYuqnqvadYs1oE4QLCdjmJ7`

---

#### Level 29 -> 30

There is a git repository at ssh://bandit29-git@localhost/home/bandit29-git/repo via the port 2220. The password for the user bandit29-git is the same as for the user bandit29.

Clone the repository and find the password for the next level.

![alt text](image-58.png#center)
Before we clone the repository, we already know that we have to make a temporary directory because we don't have permission to write in the home directory.

<br>

![alt text](image-59.png#center)
after cloning the repository, I navigated inside the repo folder and there was **README.md** file just like the two previous questions. This time there was a message saying **no passwords in production!** I thought the message implies there are more branches to this repo.

<br>

![alt text](image-60.png#center)
As you can see from the screenshot above, my theory was correct. The difference between `git branch` and `git branch -a`is that the former only shows local branches and the latter shows not only local but also remote branches.

<br>

![alt text](image-61.png#center)
I switched branch to `origin/dev` by running `git checkout origin/dev` command and checked out the content of README.md file and it contained the password for the next level as expected.

<br>

**_password_**: `qp30ex3VLz5MDG1n91YowTv4Q8l7CDZL`

---

#### Level 30 -> 31

There is a git repository at ssh://bandit30-git@localhost/home/bandit30-git/repo via the port 2220. The password for the user bandit30-git is the same as for the user bandit30.

Clone the repository and find the password for the next level.

![alt text](image-62.png#center)
Just like we did for the past few `git` related questions, we first have to create a **tmp** directory in order to clone the repo because we do not have the permission to **_write_** in the home directory.

<br>

![alt text](image-63.png#center)
the **README.md** file does not have any useful info but says that it's an empty file.

<br>

![alt text](image-65.png#center)

`git log` and `git branch -a` commands also did not reveal any useful information. I was absolutely lost and didn't know how to proceed from here.

<br>

![alt text](image-66.png#center)

I researched online and found that there's `git tag` command that's used to create, list, delete, or verify tags in a Git repository and tags are often used to mark specific points in a repo's history, like releases or important milestones.

`git tag` command revealed a tag named **secret**. In order to display the content, we have to use `git show` command.

`git show` command is used to display detailed information about various Git objects, such as commits, tags, or trees.

If you have annotated a tag, this command will display the tag's metadata.

<br>

**_password_**: `fb5S2xb7bRyFmAvQYQGEqsbhVyJqhnDy`

---

#### Level 31 -> 32

There is a git repository at ssh://bandit31-git@localhost/home/bandit31-git/repo via the port 2220. The password for the user bandit31-git is the same as for the user bandit31.

Clone the repository and find the password for the next level.

![alt text](image-67.png#center)
Same steps as before

<br>

![alt text](image-68.png#center)
This time the **README.md** file tells us to push a file to the remote repository.

<br>

![alt text](image-69.png#center)
Created a file named **key.txt** with the message, **_May I come in?_**

<br>

![alt text](image-70.png#center)
However, when pushed to master branch, it returns **Everything up-to-date**. This is strange because I definitely created a new file and made some modifications to the repo.

<br>

![alt text](image-71.png#center)
The command `ls -la` revealed that there was **.gitignore** file and it's ignoring every file with **.txt** extension.

A **.gitignore** file is used in Git to specify files and directories that should be ignored by Git. Any files or directories listed in a **.gitignore** file will not be tracked by Git, even if they are in your project directory.

<br>

![alt text](image-72.png#center)
replaced **.txt** with whitespaces.

<br>

![alt text](image-73.png#center)
Now the changes I made are being tracked by Git.

<br>

![alt text](image-74.png#center)
When pushed to the remote **_master_** branch, it returned the password for the next level.

<br>

**_password_**: `3O9RfhqyAlVBEZpVb6LYStshZoqoSx5K`

---

#### Level 32 -> 33

After all this git stuff, it’s time for another escape. Good luck!

![alt text](image-75.png#center)
This level looked very different from the previous questions. The shell says **WELCOME TO THE UPPERCASE SHELL**.

<br>

![alt text](image-76.png#center)
just like its name implied, the shell converts every input to uppercase letters. Because of that reason, every command did not work.

<br>

![alt text](image-77.png#center)
I tried the `$0` command since it does not contain any letter, there's nothing to convert to uppercase letters.

`$0` is a special parameter. It represents the name of the script or shell being executed. When you type `$0`, it might return the name of the shell or script that is running.

In our case, it seems to have triggered a change in the shell environment because the prompt changed from `>>` to `$`.

And now the commands worked as it was supposed to. It's interesting that `whoami` returned that we are currently **bandit33** meaning that we can directly access the bandit33's password as the current user.

<br>

![alt text](image-78.png#center)

<br>

**_password_**: `tQdtbs5D5i2vJwkO8mEyYEyTL8izoeJ0`

---

#### Level 33 -> 34

At this moment, level 34 does not exist yet.

![alt text](image-79.png#center)

YAY!
