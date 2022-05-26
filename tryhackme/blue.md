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


==- Reveal Answer
3
===