---
tags: [hackthebox, reversing, very easy]
categories: [hackthebox, reversing, very easy]
icon: chevron-right
author: Ivo Muijtjens
date: 2022-05-25
---
![](/static/headers/reverse.png)

# Behind the Scenes

After struggling to secure our secret strings for a long time, we finally figured out the solution to our problem: Make decompilation harder. It should now be impossible to figure out how our programs work!

---

# Writeup
Start by downloading the file `Behind the Scenes.zip` from the HackTheBox challenge onto your Kali Linux guest system. The first thing we do is running the code and see what happens.

    ┌──(imuijtjens㉿kali)-[~/Downloads]
    └─$ ./behindthescenes                  
    ./challenge <password>

We can determine it expects us to pass through a password argument. The next step is to verify what information the `strings` command reveals.

    ┌──(imuijtjens㉿kali)-[~/Downloads]
    └─$ strings behindthescenes -d     
    /lib64/ld-linux-x86-64.so.2
    libc.so.6
    strncmp
    puts
    __stack_chk_fail
    printf
    strlen
    sigemptyset
    memset
    sigaction
    __cxa_finalize
    __libc_start_main
    GLIBC_2.4
    GLIBC_2.2.5
    _ITM_deregisterTMCloneTable
    __gmon_start__
    _ITM_registerTMCloneTable
    u+UH
    []A\A]A^A_
    ./challenge <password>
    > HTB{%s}
    :*3$"

Now we learned the flag should be `HTB{%s}` where the value for `%s` is still unknown. Let's see if `ltrace` gives us more insights.

    ┌──(imuijtjens㉿kali)-[~/Downloads]
    └─$ ltrace ./behindthescenes
    --- SIGILL (Illegal instruction) ---
    --- SIGILL (Illegal instruction) ---
    ./challenge <password>
    --- SIGILL (Illegal instruction) ---
    +++ exited (status 1) +++

We receive a `SIGILL`, which is a signal that the program tried to execute an illegal instruction. I'd like to see the program in `hexeditor` to reveal more information.

    ┌──(imuijtjens㉿kali)-[~/Downloads]
    └─$ hexeditor behindthescenes

Let's search in the hexeditor by pressing <kbd>CTRL</kbd> + <kbd>W</kbd> and search for text string `challenge`, the argument we found in the first step.  It looks like this reveals our password and HackTheBox flag.

    00002000  01 00 02 00  2E 2F 63 68   61 6C 6C 65  6E 67 65 20       ...../challenge 
    00002010  3C 70 61 73  73 77 6F 72   64 3E 00 49  74 7A 00 5F       <password>.Itz._
    00002020  30 6E 00 4C  79 5F 00 55   44 32 00 3E  20 48 54 42       0n.Ly_.UD2.> HTB
    00002030  7B 25 73 7D  0A 00 00 00   01 1B 03 3B  4C 00 00 00       {%s}.......;L...

==- Reveal Flag
HTB\{Itz_0nLy_UD2\}
===