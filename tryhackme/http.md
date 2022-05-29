---
tags: [tryhackme, web, very easy]
categories: [tryhackme, web, very easy]
icon: chevron-right
author: Ivo Muijtjens
date: 2022-05-29
---
![](/static/headers/http.png)

# Web Fundamentals

Learn about how you request content from a web server using the HTTP protocol. This is a very easy room explaining how the world wide web works. Check the video from TryHackMe for a very detailed walkthrough.

[!embed](https://www.youtube.com/watch?v=XZyapIKV3Rw)

---

# Writeup

### Task 1: What is HTTP(S)?

**What does HTTP stand for?**

HyperText Transfer Protocol

**What does the S in HTTPS stand for?**

Secure

**What does the S in HTTPS stand for?**

Click on the lock to examine the SSL certificate. It will pop-up the first flag.

![Examine the SSL certificate](/static/images/ssl.png)

==- Reveal Flag 1
THM{INVALID_HTTP_CERT}
===

### Task 2: Requests And Responses

**What HTTP protocol is being used in the above example?**

HTTP/1.1

**What response header tells the browser how much data to expect?**

Content-Length

### Task 3: HTTP Methods

**What method would be used to create a new user account?**

POST

**What method would be used to update your email address?**

PUT

**What method would be used to remove a picture you've uploaded to your account?**

DELETE

**What method would be used to view a news article?**

GET

### Task 4: HTTP Status Codes

**What response code might you receive if you've created a new user or blog post article?**

201

**What response code might you receive if you've tried to access a page that doesn't exist?**

404

**What response code might you receive if the web server cannot access its database and the application crashes?**

503

**What response code might you receive if you try to edit your profile without logging in first?**

401

### Task 5: Headers

**What header tells the web server what browser is being used?**

User-Agent

**What header tells the browser what type of data is being returned?**

Content-Type

**What header tells the web server which website is being requested?**

Host

### Task 6: Cookies

**Which header is used to save cookies to your computer?**

Set-Cookie

### Task 7: Making Requests

**Make a GET request to /room**

Change the method to `GET` and the URL to `/room`. Flag will be displayed in the response.

![GET request](/static/images/get.png)

==- Reveal Flag 1
THM{YOU'RE_IN_THE_ROOM}
===

**Make a GET request to /blog and using the gear icon set the id parameter to 1 in the URL field**

Change the method to `GET` and the URL to `/blog`. Change the parameter `id` to `1`. Flag will be displayed in the response.

![GET request with parameter](/static/images/getblog.png)

==- Reveal Flag 2
THM{YOU_FOUND_THE_BLOG}
===

**Make a DELETE request to /user/1**

Change the method to `DELETE` and the URL to `/user/1`. Flag will be displayed in the response.

![DELETE request](/static/images/delete.png)

==- Reveal Flag 3
THM{USER_IS_DELETED}
===

**Make a PUT request to /user/2 with the username parameter set to admin**

Change the method to `PUT` and the URL to `/user/2`.  Change the parameter `username` to `admin`. Flag will be displayed in the response.

![PUT request](/static/images/put.png)

==- Reveal Flag 4
THM{USER_HAS_UPDATED}
===

**POST the username of thm and a password of letmein to /login**

Change the method to `POST` and the URL to `/login`.  Change the parameter `username` to `thm`, and the parameter `password` to `letmein`. Flag will be displayed in the response.

![POST request](/static/images/post.png)

==- Reveal Flag 5
THM{HTTP_REQUEST_MASTER}
===

!!!success
Congratiulations, you've completed the room.
!!!