+++ 
draft = true
date = 2018-10-12T15:59:40+01:00
title = "Lin.Security From VulnHub"
slug = "" 
tags = []
categories = []
thumbnail = "images/tn.png"
description = "The solutions I used to escalate in Lin.Security from Vulnhub"
+++

So this is my first writeup on a box from [Vulnhub](https://www.vulnhub.com). I decided to tackle [Lin.Security](https://www.vulnhub.com/entry/linsecurity-1,244/) to flex my privesc muscles and see what I could do with as little googling as I could.

## Ready
I fired up the VM inside VirtualBox on a host-only network (standard practice I hear you say), and did the same with my Kali Box. Once they had booted I logged into Kali and did some recon to locate my new target.

## Aim
```
$ nmap -sP 10.10.10.0/24
Nmap scan report for 10.10.10.1
... Other results ...
Nmap scan report for lin.security (10.10.10.100)
Host is up (0.0019s latency).
MAC Address: 08:00:27:A4:55:2C (Oracle VirtualBox virtual NIC)
... More Results ...
Nmap done: 256 IP addresses (4 hosts up) scanned in 1.93 seconds
```
There were some Virtualbox specific entries that came up in the results as well as the Kali box, but the key thing was we had our target. Time to press on.

To find out what there is on offer to attack on this box I then ran a more indepth nmap scan. Taking a leaf out off the [@ippsec](https://twitter.com/ippsec) playbook on his HTB videos, this was the nmap scan...

```
nmap -sS -sC -oA lin.security -A -O  -p- 10.10.10.100
# Nmap 7.70 scan initiated Fri Oct 12 13:26:31 2018 as: nmap -sS -sC -oA lin.security -A -O -p- 10.10.10.100
mass_dns: warning: Unable to determine any DNS servers. Reverse DNS is disabled. Try using --system-dns or specify valid servers with --dns-servers
Nmap scan report for lin.security (10.10.10.100)
Host is up (0.00047s latency).
Not shown: 65528 closed ports
PORT      STATE SERVICE  VERSION
22/tcp    open  ssh      OpenSSH 7.6p1 Ubuntu 4 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 7a:9b:b9:32:6f:95:77:10:c0:a0:80:35:34:b1:c0:00 (RSA)
|   256 24:0c:7a:82:78:18:2d:66:46:3b:1a:36:22:06:e1:a1 (ECDSA)
|_  256 b9:15:59:78:85:78:9e:a5:e6:16:f6:cf:96:2d:1d:36 (ED25519)
111/tcp   open  rpcbind  2-4 (RPC #100000)
| rpcinfo: 
|   program version   port/proto  service
|   100000  2,3,4        111/tcp  rpcbind
|   100000  2,3,4        111/udp  rpcbind
|   100003  3           2049/udp  nfs
|   100003  3,4         2049/tcp  nfs
|   100005  1,2,3      42471/udp  mountd
|   100005  1,2,3      48015/tcp  mountd
|   100021  1,3,4      43803/tcp  nlockmgr
|   100021  1,3,4      52113/udp  nlockmgr
|   100227  3           2049/tcp  nfs_acl
|_  100227  3           2049/udp  nfs_acl
2049/tcp  open  nfs_acl  3 (RPC #100227)
35181/tcp open  mountd   1-3 (RPC #100005)
35649/tcp open  mountd   1-3 (RPC #100005)
43803/tcp open  nlockmgr 1-4 (RPC #100021)
48015/tcp open  mountd   1-3 (RPC #100005)
MAC Address: 08:00:27:A4:55:2C (Oracle VirtualBox virtual NIC)
Device type: general purpose
Running: Linux 3.X|4.X
OS CPE: cpe:/o:linux:linux_kernel:3 cpe:/o:linux:linux_kernel:4
OS details: Linux 3.2 - 4.9
Network Distance: 1 hop
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

TRACEROUTE
HOP RTT     ADDRESS
1   0.47 ms lin.security (10.10.10.100)

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Fri Oct 12 13:26:42 2018 -- 1 IP address (1 host up) scanned in 11.49 seconds
```

## Fire

Ways in:
- nfspysh to add SSH keypair to /home/peter, then ssh as Peter
- bob
    - SSH in with keypair from Website
    - identify applications which can be run as sudo with sudo -ll
        - privesc with apps such as vi, zsh etc. using sudo


I re-read the brief on VulnHub about this box and noticed on the second reading that it gives you some starter creds, its worth noting at this point I had missed these entirely and made a tragic mistake of going off brief. **There is a lesson here, read and re-read your scoping brief, it will save a lot of time, effort and an ear chewing from the client when you submit a report on something out of scope.**

With creds in hand I ssh'd to Lin.Security as Bob. 

