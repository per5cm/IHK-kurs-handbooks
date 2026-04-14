# Kali Linux Vm

**Environment:** Oracle Virtual Box, Kali ISO 

## Software, ISO
---

- Download Oracle Virtual Box
- Download latest Kali Linux ISO image.

---

## Network Adapters

---

- Network -> NAT Network for WiFi connection

1. File 
2. Tools
3. Network
4. Nat Networks
5. Create click OK!
6. Settings switch to NAT Network

> ping 8.8.8.8 c- 4 or ping google.com c- 4 - to check network connection

- Bridged -> for cable connection

1. Promiscious mode: Allow All.

---

## Update Basics

---
```bash
sudo apt update && sudo apt full-upgrade -y
# (remove uncecery installer files)
sudo apt autoremove 
# after, make a clean snapshot.
```
---

## Basic Linux Commands

---
```bash
# (creates a file.)
touch file1 
# (writes a text inside a file1.)
echo Today is a really good day. > file1
# (shows content of a file1.)
cat file1
# (creates and opens file edittor.)
nano file2
# (creates python file with nano file3.py you can write directly python code in edittor.)
python file3.py
# (go back in directory.)
cd ..
# (list items in directory.)
ls 
# (copy a file)
cp file3.py file4.py
cp file3.py /home/alchemist/Desktop/ourcopy.py
# (remove a file)
rm file4.py
# (remove a folder first make a folder with - mkdir Folder)
rm Folder -r
# (remove whole content from a folder, it removes any content from ur current terminal directory)
rm * -r
```
---

## Basic Linux Network Commands

---
```bash
# (display ip configuration)
sudo ifconfig
ip addr show
# (become root user)
sudo su
# (exit root user)
exit
```
---

## Active, Passive Information Gathering

**Active** Active Kali Linux --> Information <--> Target Website --> Kali Linux.
**Passive** Pasive Kali Linux --> Middle Source <--> Target Website --> Kali Linux.

### Things to search for

---

1. IP Address
2. Email
3. Technologies

---

### Active Information gathering, tools and methods

---

1. Pick a any website
2. Ping there  Web Address in Kali Linux Terminal (some websites have ping block but u get IP address)
```bash
# random university website
ping etf.bg.ac.rs

# ping facebook
ping facebook.com
```
3. An other tool is ns-lookup
```bash
# random university website
nslookup etf.bg.ac.rs

# ping facebook
nslookup facebook.com
```
3. google ip checkers, you can get more information, physical address, provider etc.
4. you can use terminal command whois
```bash
# random university website
whois etf.bg.ac.rs

# ping facebook
whois facebook.com
```
4. using whatweb, its built in Kali tool (see help for more command infos)
```bash
# random university website
whatweb arh.bg.ac.rs

# more readable version (verbose query)
whatweb arh.bg.ac.rs -v
```
5. whatweb more aggresive scaning version (stealth for just baisc, aggresive for actual pen test)
```bash
# scan ur own ip address first because its your own
sudo ifconfig

# than increase scan aggression and enter ip range
whatweb 192.168.178.1-192.168.178.255 --aggression 3 -v

# same command with no error displaying
whatweb 192.168.178.1-192.168.178.255 --aggression 3 -v --no-errors

# save scanned ip address info into a file
whatweb 192.168.178.1-192.168.178.255 --aggression 3 -v --no-errors --log-verbose=Results
```

> Sometimes Tools will break or not work! its good practice to have multiple tools.

6. gathering emails using harverster tools of Kali linux.
```bash
# in terminal looking up available commands
theHarvester --help

# searching for hosts usernames and emails.
theHarvester -d mas.bg.ac.rs -b all -l 50
```
> paid SaaS does the same output as harverster hunter.io
---

### Downloading Tools, GitHub

**Why** just google -> Information Gathering Tools GitHub.

1. RED_HAWK all in one tool for pentesters.
```bash
# install via github link
git clone https://github.com/Tuhinshubhra/RED_HAWK

# switch to red hawk folder
cd RED_HAWK

# search the folder
ls

# pick an installation file
php rhawk.php

# install red hawk updates, when u run red hawk from terminal
fix
```
> later we will download bunch of other usefull tools.