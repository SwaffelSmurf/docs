---
tags: [reversing, very easy]
---

# Behind the Scenes

#### Challenge Description
After struggling to secure our secret strings for a long time, we finally figured out the solution to our problem: Make decompilation harder. It should now be impossible to figure out how our programs work!

---

# Writeup
Start by downloading the files `Behind the Scenes.zip` from the HackTheBox challenge onto your Kali Linux guest system. The first thing we do is running the code and see what happens.

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

Now we learned the flag should be `HTB{%s}` where the value for `%s` is still unknown.