# [`MemLabs Lab 3 - The Evil's Den`](https://mega.nz/#!2ohlTAzL!1T5iGzhUWdn88zS1yrDJA06yUouZxC-VstzXFSRuzVg)

### Challenge Description

- A malicious script encrypted a very secret piece of information I had on my system. Can you recover the information for me please?

**Note - 1 :** This challenge is composed of only 1 flag. The flag split into 2 parts.

**Note - 2 :** You'll need the first half of the flag to get the second.

- You will need this additional tool to solve the challenge.

```
$ sudo apt install steghide
```

- The flag format for this lab is : `inctf{s0me_l33t_Str1ng}`

# Solution

### Image Identification 

- When a **Memory dump** is taken, it is extremely important to know the information about the **operating system** that was in use.
- **Volatility** will try to read the image and suggest the related profiles for the given memory dump. 
- The `imageinfo` plugin is used to identify the **Operating System, service pack, and hardware architecture (32 or 64 bit) DTB address, time the sample was collected** etc.

```
/mnt/e/Tools/V.exe -f "Lab 3.raw" imageinfo
```

![](https://github.com/a3X3k/MemLabs/blob/main/Lab%203/Assets/1.png?raw=true)
