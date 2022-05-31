---
tags: [tryhackme, web, very easy]
categories: [tryhackme, web, very easy]
icon: chevron-right
author: Ivo Muijtjens
date: 2022-05-31
---
![](/static/headers/cyberheroes.png)

# CyberHeroes

Want to be a part of the elite club of CyberHeroes? Prove your merit by finding a way to log in. This is a very easy room. Try to bypass the login screen.

---

# Writeup

### Uncover the flag!

This room is very easy to complete. I've started by opening the URL in the browser. The page source of the `index.html` page doesn't reveal anything interesting. I clicked the login button, and again opened the page source. At first there is the login screen, which contains 2 input fields: username and password. Under the form is a button which the function `authenticate`.

    <section id="hero" class="d-flex flex-column justify-content-center align-items-center">
      <div class="hero-container">
        <br><br><br><br>
        <div class="">
          <div class="form">
          <h4 id="flag"></h4>
            <form id="todel"class="">
              <div class="section-title">
                <h2>Login</h2>
                <h4>Show your hacking skills and login to became a CyberHero ! :D</h4>
              </div>
              <input type="text" id="uname" placeholder="username"/>
              <input type="password" id="pass" placeholder="password"/>
            </form>
            <button id="rm" onclick="authenticate()">login</button>
          </div>
        </div>
      </div>
    </section>

The `<script>` tag is used to embed client-side JavaScript for the function `authenticate`. Let's walk through the script to understand what's happening.

    <script>
        function authenticate() {
        a = document.getElementById('uname')
        b = document.getElementById('pass')
        const RevereString = str => [...str].reverse().join('');
        if (a.value=="h3ck3rBoi" & b.value==RevereString("54321@terceSrepuS")) { 
            var xhttp = new XMLHttpRequest();
            xhttp.onreadystatechange = function() {
            if (this.readyState == 4 && this.status == 200) {
                document.getElementById("flag").innerHTML = this.responseText ;
                document.getElementById("todel").innerHTML = "";
                document.getElementById("rm").remove() ;
            }
            };
            xhttp.open("GET", "RandomLo0o0o0o0o0o0o0o0o0o0gpath12345_Flag_"+a.value+"_"+b.value+".txt", true);
            xhttp.send();
        }
        else {
            alert("Incorrect Password, try again.. you got this hacker !")
        }
        }
    </script>

Variable `a` is the username (uname) from the form.
Variable `b` is the password (pass) from the form.

The script states that if variable `a` equals to `h3ck3rBoi` and variable `b` equals to the reverse string of `54321@terceSrepuS` you'll be authenticated. To reverse the string you can Google for online tools, I'm using the terminal within Kali Linux.

    echo "54321@terceSrepuS" | rev

With that done we have a username and password.

Username: `h3ck3rBoi`

Password: `SuperSecret@12345`

Now we're able to login on the website to reveal the flag.

![Flag revealed](/static/images/loggedin.png)

==- Reveal Flag
flag\{edb0be532c540b1a150c3a7e85d2466e\} 
===

!!!success
Congratiulations, you've completed the room.
!!!