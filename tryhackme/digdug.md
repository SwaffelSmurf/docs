---
tags: [tryhackme, dns, very easy]
categories: [tryhackme, dns, very easy]
icon: chevron-right
author: Ivo Muijtjens
date: 2022-05-31
---
![](/static/headers/dig.png)

# Dig Dug

Turns out this machine is a DNS server - it's time to get your shovels out. This is a very easy room to get familiar with DNS requests. Let's dig a dug.

---

# Writeup

### Retrieve the flag from the DNS server!

This task is very simple. Just read the description and think clearly. First thing I did whas writing down the given information:

`<IP_ADDRESS>` is our DNS server

`dig` is the command we use to gather DNS information

`givemetheflag.com` is the domain name to investigate

Now that we know that, let's put it together in a one-liner:

    dig @<IP_ADDRESS> givemetheflag.com

The `@` parameter defines the DNS server we wanna use. This will provide us the information we need. The flag is in a TXT-record:

    ; <<>> DiG 9.18.1-1-Debian <<>> @10.10.36.217 givemetheflag.com
    ; (1 server found)
    ;; global options: +cmd
    ;; Got answer:
    ;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 37961
    ;; flags: qr aa; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 0

    ;; QUESTION SECTION:
    ;givemetheflag.com.             IN      A

    ;; ANSWER SECTION:
    givemetheflag.com.      0       IN      TXT     "<FLAG>"

    ;; Query time: 31 msec
    ;; SERVER: 10.10.36.217#53(10.10.36.217) (UDP)
    ;; WHEN: Tue May 31 13:38:59 EDT 2022
    ;; MSG SIZE  rcvd: 86

==- Reveal Flag
flag\{0767ccd06e79853318f25aeb08ff83e2\}
===

!!!success
Congratiulations, you've completed the room.
!!!