---
icon: sun
---
![](/static/headers/mandiant.png)

# FlareVM

FlareVM is a reverse engineering and malware analysis distribution on Windows platform. It includes a lot of tools for security researchers. In this post I'll explain how to install a FlareVM on a clean Windows 11 installation.

---

# Installation

#### Requirements

- 60 GB Hard Drive
- 2 GB RAM

#### Windows 11 Installation

##### Pre-installation

- Make sure a clean Windows 11 VM is already installed
- Disable Windows Defender
- Make sure the following settings are configured:
    - Disable real-time protection
    - Disable cloud-delivered protection
    - Disable automatic sample submission
    - Add the directory `C:\` to the exclusion list
- Take a snapshot of your VM

##### Installation

- Download and copy [install.ps1](https://github.com/fireeye/flare-vm/raw/master/install.ps1) onto your new VM
- Open PowerShell as an Administrator
- Unblock the install file by running:
    - `Unblock-File .\install.ps1`
- Enable script execution by running:
    - `Set-ExecutionPolicy Unrestricted`
- Finally, execute the installer script as follow:
    - `.\install.ps1`
    - You can also pass your password as an argument: `.\install.ps1 -password <password>`

##### Post-installation
- If the background was not successfully set:
    - Navigate to `%PROGRAMDATA%\chocolatey\lib\flarevm.win10.config.flare\tools` and right click `flarevm.png`
    - Right click `flarevm.png` and select `Set as desktop background`
- Take a post-installation snapshot

!!!warning Warning
Don't forget to update your VM's network settings to `Host-Only` so that vulnerable programs cannot hop to your host system.
!!!

[!ref target="blank" text="Mandiant FlareVM GitHub"](https://github.com/mandiant/flare-vm)

---

# Updates

!!!primary Primary
In order to install updates, make sure you update your VM's netwerk settings to `NAT` to restore your internet connection.
!!!

You can customize your distribution by installing additional packages. For an overview of all available packages, check the repository on [Chocolatey](https://community.chocolatey.org/packages). For example, to install Google Chrome, use the following command:

    cinst googlechrome

It's also important to keep your distribution up-to-date. Use the following command to update your installed packages:

    cup all