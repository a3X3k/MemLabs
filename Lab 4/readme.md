# [`MemLabs Lab 4 - Obsession`](https://mega.nz/#!Tx41jC5K!ifdu9DUair0sHncj5QWImJovfxixcAY-gt72mCXmYrE)

- MemLabs is an educational, introductory set of CTF-styled challenges made by @stuxnet9999 (aka Abhiram Kumar) which is aimed to encourage students, security researchers, and also CTF players to get started with the field of Memory Forensics.

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

- From the challenge description we shall understand that we have to find the important file which was deleted.
- **Volatility** has the `mftparser` plugin which scans for potential **Master File Table (MFT)** entries in memory and prints out information for certain attributes.
- It displays the **deleted files or files modified** and **creation Date** etc.

```
/mnt/e/Tools/V.exe -f "Lab 4.raw" --profile=Win7SP1x64 mftparser --output=text -D . --output-file=file.text
```

![](https://github.com/a3X3k/MemLabs/blob/main/Lab%204/Assets/2.png)

- The output file gives all information from the output of the `mftparser`.

![](https://github.com/a3X3k/MemLabs/blob/main/Lab%204/Assets/3.png)

- In that we can find that there is a file named **Important.txt** which has the flag.

```
inctf{1_is_n0t_EQu4l_7o_2_bUt_th1s_d0s3nt_m4ke_s3ns3}
```

### Volatility Commands Reference - [`📖`](https://github.com/volatilityfoundation/volatility/wiki/Command-Reference#dumpfiles) [`📖`](https://www.codersnoon.com/2021/01/volatility-cheatsheet-memory-forensics.html) 



