# [`MemLabs Lab 3 - The Evil's Den`](https://mega.nz/#!2ohlTAzL!1T5iGzhUWdn88zS1yrDJA06yUouZxC-VstzXFSRuzVg)

- MemLabs is an educational, introductory set of CTF-styled challenges made by @stuxnet9999 (aka Abhiram Kumar) which is aimed to encourage students, security researchers, and also CTF players to get started with the field of Memory Forensics.

### Challenge Description

> A malicious script encrypted a very secret piece of information I had on my system. 
> Can you recover the information for me please?

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

### Analysis

- From the challenge description **A malicious script** we shall understand that there should be some `script` associated.
- So I tried to look the executed commands in **cmd** using the `cmdscan` plugin, but nothing useful.
- So the next analysis I did was to identify the presence of any suspicious processes and to view any high-level running processes.
- The `pslist` plugin will list the processes of a system.

```
/mnt/e/Tools/V.exe -f "Lab 3.raw" --profile=Win7SP1x86_23418 pslist
```

![](https://github.com/a3X3k/MemLabs/blob/main/Lab%203/Assets/2.png?raw=true)

![](https://github.com/a3X3k/MemLabs/blob/main/Lab%203/Assets/3.png?raw=true)

- We shall see that, there is a `notepad` running.
- So I used `cmdline` plugin to list the Notepad's command line arguments with the help of the `PID`. 

```
/mnt/e/Tools/V.exe -f "Lab 3.raw" --profile=Win7SP1x86_23418 cmdline -p 3736,3432
```

![](https://github.com/a3X3k/MemLabs/blob/main/Lab%203/Assets/4.png?raw=true)

- Now, we found out that there is a **script** and a **text** file.
- Next step is to extract/dump the required files.
- `dumpfiles` plugin is used to extract the **memory mapped** and **cached files**.
- Inorder to dump the file we must know the `physical address` of the file ( ie ) the location of the file.
- `filescan` plugin is used to find **FILE_OBJECTs** in physical memory.
- Since we know the files which is required we shall **grep** it directly by specifying the file name.

```
/mnt/e/Tools/V.exe -f "Lab 3.raw" --profile=Win7SP1x86_23418 filescan | grep "evilscript.py\|vip.txt"
```

![](https://github.com/a3X3k/MemLabs/blob/main/Lab%203/Assets/5.png?raw=true)

- Now we got the physical address of the file and so that we shall **dump** the file using the physical address as offset.

```
/mnt/e/Tools/V.exe -f "Lab 3.raw" --profile=Win7SP1x86_23418 dumpfiles -Q 0x000000003de1b5f0 -D .

/mnt/e/Tools/V.exe -f "Lab 3.raw" --profile=Win7SP1x86_23418 dumpfiles -Q 0x000000003e727e50 -D .
```

![](https://github.com/a3X3k/MemLabs/blob/main/Lab%203/Assets/6.png?raw=true)

### [`Evilscript.py`](https://github.com/a3X3k/MemLabs/blob/main/Lab%203/Assets/Script.py)

```py
import sys
import string

def xor(s):
	a = ''.join(chr(ord(i)^3) for i in s)
	return a

def encoder(x):
	return x.encode("base64")

if __name__ == "__main__":
	f = open("C:\\Users\\hello\\Desktop\\vip.txt", "w")
	arr = sys.argv[1]
	arr = encoder(xor(arr))
	f.write(arr)
	f.close()
```

### [`Vip.txt`](https://github.com/a3X3k/MemLabs/blob/main/Lab%203/Assets/Vip.txt)

```
am1gd2V4M20wXGs3b2U=
```

- From the script we shall understand that **Its taking a Input, and Xoring it with Key 3, Encoding the output to Base64 and writing it in the vip.txt file**.
- Now the way to get the original text is to backtrack the commands that were executed.

![](https://github.com/a3X3k/MemLabs/blob/main/Lab%203/Assets/7.png?raw=true)

- We shall get the first part of the flag.

```
inctf{0n3_h4lf
```

- Now that we got 1 part of the flag and there is one more important point in the description `You will need this additional tool to solve the challenge - Steghide`.
- So there should be something hidden in `image` and `audio` files.
- So let's grep for the image and audio files in filescan.

```
/mnt/e/Tools/V.exe -f "Lab 3.raw" --profile=Win7SP1x86_23418 filescan | grep -i ".jpg\|.jpeg\|.png\|.wav\|.au"
```

![](https://github.com/a3X3k/MemLabs/blob/main/Lab%203/Assets/8.png?raw=true)

- We shall see that there is a file called `suspision1.jpeg`.
- So let's **dump** it using `dumpfiles`.

```
/mnt/e/Tools/V.exe -f "Lab 3.raw" --profile=Win7SP1x86_23418 dumpfiles -Q 0x0000000004f34148 -D .
```

- Now we get the [`Image`](https://github.com/a3X3k/MemLabs/blob/main/Lab%203/Assets/1.jpeg).
- Let's run steghide command on the image.

```
steghide extract -sf 1.jpeg
```

![](https://github.com/a3X3k/MemLabs/blob/main/Lab%203/Assets/9.png?raw=true)

- When it prompts for password, use the first half of the flag as its password.
- Finally we get the **secret.txt**.

### [`Secret.txt`](https://github.com/a3X3k/MemLabs/blob/main/Lab%203/Assets/secret%20text)

```
_1s_n0t_3n0ugh}
```

- Concatenating both gives the flag.

### [`inctf{0n3_h4lf_1s_n0t_3n0ugh}`]()

### Volatility Commands Reference - [`📖`](https://github.com/volatilityfoundation/volatility/wiki/Command-Reference#dumpfiles) [`📖`](https://www.codersnoon.com/2021/01/volatility-cheatsheet-memory-forensics.html) 


