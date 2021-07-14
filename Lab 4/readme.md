# [`MemLabs Lab 4 - Obsession`](https://mega.nz/#!Tx41jC5K!ifdu9DUair0sHncj5QWImJovfxixcAY-gt72mCXmYrE)

### Challenge Description

> My system was recently compromised. 
> The Hacker stole a lot of information but he also deleted a very important file of mine. 
> I have no idea on how to recover it. 
> The only evidence we have, at this point of time is this memory dump. 
> Please help me.

- **Note :** This challenge is composed of only 1 flag.
- The flag format for this lab is: inctf{s0me_l33t_Str1ng}

# Solution

### Image Identification 

- When a **Memory dump** is taken, it is extremely important to know the information about the **operating system** that was in use.
- **Volatility** will try to read the image and suggest the related profiles for the given memory dump. 
- The `imageinfo` plugin is used to identify the **Operating System, service pack, and hardware architecture (32 or 64 bit) DTB address, time the sample was collected** etc.

```
/mnt/e/Tools/V.exe -f "Lab 4.raw" imageinfo
```

![](https://github.com/a3X3k/MemLabs/blob/main/Lab%204/Assets/1.png)
