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
1. sudo apt update && sudo apt full-upgrade -y
# (remove uncecery installer files)
2. sudo apt autoremove 
# after, make a clean snapshot.
```
---

## Basic Linux Commands

---
```bash
# (creates a file.)
1. touch file1 
# (writes a text inside a file1.)
2. echo Today is a really good day. > file1
# (shows content of a file1.)
3. cat file1
# (creates and opens file edittor.)
4. nano file2
# (creates python file with nano file3.py you can write directly python code in edittor.)
5. python file3.py
# (go back in directory.)
6. cd ..
# (list items in directory.)
7. ls 
# (copy a file)
8. cp file3.py file4.py || cp file3.py /home/alchemist/Desktop/ourcopy.py
# (remove a file)
9. rm file4.py
# (remove a folder first make a folder with - mkdir Folder)
10. rm Folder -r
# (remove whole content from a folder, it removes any content from ur current terminal directory)
11. rm * -r
```
---

## Basic Linux Network Commands

---
```bash
# (display ip configuration)
1. sudo ifconfig
# (navigate to root terminal directory)
2. sudo su
# (exit root terminal directory)
3. exit
```
---