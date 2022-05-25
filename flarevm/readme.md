---
icon: sun
---
![](/static/headers/flare.png)

# FlareVM

FlareVM is a reverse engineering and malware analysis distribution on Windows platform. It includes a lot of tools for security researchers. In this post I'll explain how to install a FlareVM on a clean Windows 11 installation.

---

# Installation

#### Requirements

- 60 GB Hard Drive
- 2 GB RAM

#### Windows 11 Installation

### Pre-installation

- Make sure a clean Windows 11 VM is already installed
- Disable Windows Defender
- Make sure the following settings are configured:
    - Disable real-time protection
    - Disable cloud-delivered protection
    - Disable automatic sample submission
    - Add the directory `C:\` to the exclusion list
- Take a snapshot of your VM