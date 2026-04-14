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

[← Back to Index](../index.md)