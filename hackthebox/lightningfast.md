---
tags: [hackthebox, gamepwn, medium]
categories: [hackthebox, gamepwn, medium]
icon: chevron-right
author: Ivo Muijtjens
date: 2022-10-19
---

![](/static/headers/lightningfast.png)

# LightningFast

Things in this game happen as fast as a lightning... Enjoy our new self-hosted game!

---

# Writeup

Start by downloading the file `LightningFast.zip` from the HackTheBox challenge onto your Kali Linux guest system. Start the game by running `LightningFast.exe` and click around to see what is does. Note the buttons in the menu:

- Play
- Options
- Exit
- Shop

Now start the instance on the Hack The Box page, and wait till you get an IP-address and port number. Under options, fill in the IP-address and port number. For example: `142.93.35.129:31302`. Click `Save` and go back to the menu. Click around to see how the game works. Note how many points you need to buy the flag in the shop.

## Burp Suite

So we know this game uses a backend webserver to save the score. In order to intercept and handle our requests we can use Burp Suite. Under `Proxy` and then `Options` add a proxy listener. Bind it to port 80 of the loopback interface.

![Proxy listener](/static/images/listener.png)

On the `Request handling` tab, redirect your requests to the game server. This is the IP-address and port number from your Hack The Box instance.

![Proxy listener](/static/images/redirect.png)

Now setup the proxy ingame to your created Burp proxy listener `127.0.0.1:80` and click `Save`.

![Proxy ingame](/static/images/proxy.png)

Turn on intercept and start playing the game. Forward the first packages untill a POST request shows up with your score. Note that the score is sent in JSON format. Edit the score so that it's enough to purchase the flag. In this example I put in a score of `1000007`. Then forward the package. The request received by the listener will now be sent to the gameserver with a modified score.

![Score](/static/images/score.png)

Now purchase the flag in the store. Your HTB\{\} flag will be printed under the back button. Make sure to make a screen capture, since it will disappear in a few seconds.

![Flag](/static/images/lightningfast.png)

!!!success
Congratiulations, you've completed the challenge.
!!!
