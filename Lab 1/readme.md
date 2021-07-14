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

![](https://github.com/a3X3k/MemLabs/blob/main/Lab%201/Assets/1.png)

- From the challenge description **We suddenly saw a black window pop up with some thing being executed**, we shall understand that it's a `console shell (cmd.exe)`.
- So let's start with this hint.
- Volatility has a `cmdscan` plugin which lists the **executed commands** in the **console shell**.
- In addition to the commands entered into a shell, this plugin shows,
    - The name of the console host process **(csrss.exe or conhost.exe)**
    - The name of the application using the console (whatever process is using cmd.exe)
    - The location of the command history buffers, including the current buffer count, last added command, and last displayed command
    - The application process handle

```
/mnt/e/Tools/V.exe -f "Lab 1.raw" --profile=Win7SP1x64 cmdscan
```

![](https://github.com/a3X3k/MemLabs/blob/main/Lab%201/Assets/14.png)

- Here we shall see that there is a command called `St4G3$1` which is nothing but `Stage3$1`.
- So in order to see the output of commands executed in the **console shell**, volatility has `consoles` plugin.
- The major advantage to this plugin is that, it not only prints the commands that were typed by the attackers, but also collects the entire screen buffer (input and output).
- So basically we will be able to see the output of executed commands.

```
/mnt/e/Tools/V.exe -f "Lab 1.raw" --profile=Win7SP1x64 consoles
```

![](https://github.com/a3X3k/MemLabs/blob/main/Lab%201/Assets/9.png)

- We shall see that there is Base64 encoded text `ZmxhZ3t0aDFzXzFzX3RoM18xc3Rfc3Q0ZzMhIX0=`.
- [`Decoding`](https://www.base64decode.org/) this gives the flag.

```
flag{th1s_1s_th3_1st_st4g3!!}
```

- Now we got our first flag.
- Let's look into the next hint given in the challenge description.
- From the hint **When the crash happened, she was trying to draw something**, we shall understand that she was using some graphics editor like `Ms Paint`.
- So to confirm this, let's run the `pslist` plugin which lists all high-level running processes.

```
/mnt/e/Tools/V.exe -f "Lab 1.raw" --profile=Win7SP1x64 pslist
```

![](https://github.com/a3X3k/MemLabs/blob/main/Lab%201/Assets/15.png)

![](https://github.com/a3X3k/MemLabs/blob/main/Lab%201/Assets/16.png)

- We shall see that, the **MS Paint.exe** is being executed.
- Let's dump the Memory of this process using its **PID**.
- `memdump` plugin in extracts all **memory resident pages** in a process.

```
/mnt/e/Tools/V.exe -f "Lab 1.raw" --profile=Win7SP1x64 memdump -p 2424 -D .
```

![](https://github.com/a3X3k/MemLabs/blob/main/Lab%201/Assets/10.png)

- Now you will get the **.dmp** output file.
- At this point we have to analyse the **.dmp** file.
- After spending lot of time on googling I finally ended up in this [`Reddit Post`](https://www.reddit.com/r/netsec/comments/2x8f17/extracting_raw_pictures_from_memory_dumps/).
- As mentioned in this, after converting the `.dmp` to `.data` opening the **.data** file in **GIMP** and making some modifications in the **height, width and offset** we will be able to see the flag written in that.
- Good modifications gives good readabiltity and I was able to read it after spending some time in reading.

![](https://github.com/a3X3k/MemLabs/blob/main/Lab%201/Assets/12.png)

![](https://github.com/a3X3k/MemLabs/blob/main/Lab%201/Assets/13.png)

```
flag{G00d_BoY_good_girL}
```

- Now that we got two flags.
- One more flag is yet to be found.
- We have already done the analysis using `cmdscan`,`consoles` and `pslist`.
- Only `cmdline` plugin is left which lists all the command line arguments with the help of **PID**.

```
/mnt/e/Tools/V.exe -f "Lab 1.raw" --profile=Win7SP1x64 cmdline
```

![](https://github.com/a3X3k/MemLabs/blob/main/Lab%201/Assets/2.png)

![](https://github.com/a3X3k/MemLabs/blob/main/Lab%201/Assets/3.png)

- We shall see that there is a files named [`Important.rar`](https://github.com/a3X3k/MemLabs/blob/main/Lab%201/Assets/1.rar).
- Next step is to **extract/dump** the file.
- `dumpfiles` plugin is used to extract the memory mapped and cached files.
- Inorder to **dump** the file we must know the **physical address** of the file ( ie ) the location of the file.
- `filescan` plugin is used to find FILE_OBJECTs in physical memory.
- Since we know the files which is required we shall **grep** it directly by specifying the file name.

```
/mnt/e/Tools/V.exe -f "Lab 1.raw" --profile=Win7SP1x64 filescan | grep Important.rar
```

![](https://github.com/a3X3k/MemLabs/blob/main/Lab%201/Assets/4.png)

- Now we got the physical address of the file and so that we shall **dump** the file using the physical address as offset.

```
/mnt/e/Tools/V.exe -f "Lab 1.raw" --profile=Win7SP1x64 dumpfiles -Q 0x000000003fa3ebc0 -D .
```

![](https://github.com/a3X3k/MemLabs/blob/main/Lab%201/Assets/5.png)

- But the file is **encrypted** and luckily **7-Zip** showed an error which made the guess easier. 

![](https://github.com/a3X3k/MemLabs/blob/main/Lab%201/Assets/8.png)

- Looking the `hex dump` we shall get the **hint** for the **password**.

![](https://github.com/a3X3k/MemLabs/blob/main/Lab%201/Assets/6.png)

> **Hint**
> > Password is NTLM hash(in uppercase) of Alissa's account passwd

- So now we need the **hash** of the account.
- Inorder to extract and **decrypt cached domain credentials** stored in the **registry** we have the `hashdump` plugin.

```
/mnt/e/Tools/V.exe -f "Lab 1.raw" --profile=Win7SP1x64 hashdump
```

![](https://github.com/a3X3k/MemLabs/blob/main/Lab%201/Assets/7.png)

```
Hash - f4ff64c8baac57d22f22edc681055ba6
Upper Case - F4FF64C8BAAC57D22F22EDC681055BA6
```

- Now we will be able to extract the [`Image`](https://github.com/a3X3k/MemLabs/blob/main/Lab%201/Assets/flag3.png) which has the third flag.

```
flag{w3ll_3rd_stage_was_easy}
```

### Flags

```
flag{th1s_1s_th3_1st_st4g3!!}
flag{G00d_BoY_good_girL}
flag{w3ll_3rd_stage_was_easy}
```

### Volatility Commands Reference - [`📖`](https://github.com/volatilityfoundation/volatility/wiki/Command-Reference#dumpfiles) [`📖`](https://www.codersnoon.com/2021/01/volatility-cheatsheet-memory-forensics.html) 



