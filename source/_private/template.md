---
title: Pentesting writeups - template
date: 2024-08-01 10:00:00
categories:
  - Pentesting
  - Writeups
  - HTB
tags:
  - Pentesting
  - Writeups
  - HTB
  - Template
toc: true
---

---
> Possible template when running a penetration test on a machine.
(Summary will be shown here)

---
<!-- more -->

<br>

<!-- ~~~plain -->
## Walkthrough and interesting findings
Walkthrough:
```md

```

Interesting findings:
- 

<br>

## Loot

Credentials
| username | password | hash | service used |
| --- | --- | --- | --- |
|  |  |  |  |
|  |  |  |  |

Insights
```plain


```

<br>

## 1. Preparation
```bash Dedicated directory for the target machine
mkdir -p ~/HTB/machine
```
```bash Prepare the environment variables
export domain=example.com
export IP=10.10.10.1
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

### Port X/TCP - protocol
```bash

```

<br>

## 4. Exploitation
```bash

```

<br>

## 5. Internal enumeration
```bash

```

<br>

## 6. Privilege escalation
```bash

```

<!-- ~~~ -->

<br>

---
---