# [`MemLabs Lab  - Beginner's Luck`](https://mega.nz/#!6l4BhKIb!l8ATZoliB_ULlvlkESwkPiXAETJEF7p91Gf9CWuQI70)

- MemLabs is an educational, introductory set of CTF-styled challenges made by [`@stuxnet9999 (aka Abhiram Kumar)`](https://github.com/stuxnet999) which is aimed to encourage students, security researchers, and also CTF players to get started with the field of Memory Forensics.

### Challenge Description

> My sister's computer crashed. We were very fortunate to recover this memory dump. Your job is get all her important files from the system. From what we remember, we suddenly saw a black window pop up with some thing being executed. When the crash happened, she was trying to draw something. Thats all we remember from the time of crash.

- **Note :** This challenge is composed of 3 flags.

# Solution

### Image Identification 

- When a **Memory dump** is taken, it is extremely important to know the information about the **operating system** that was in use.
- **Volatility** will try to read the image and suggest the related profiles for the given memory dump. 
- The `imageinfo` plugin is used to identify the **Operating System, service pack, and hardware architecture (32 or 64 bit) DTB address, time the sample was collected** etc.

```
/mnt/e/Tools/V.exe -f "Lab 1.raw" imageinfo
```

### Analysis
