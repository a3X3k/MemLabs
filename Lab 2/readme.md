# [`MemLabs Lab 2 - A New World`](https://mega.nz/#!ChoDHaja!1XvuQd49c7-7kgJvPXIEAst-NXi8L3ggwienE1uoZTk)

- MemLabs is an educational, introductory set of CTF-styled challenges made by [`@stuxnet9999 (aka Abhiram Kumar)`](https://github.com/stuxnet999) which is aimed to encourage students, security researchers, and also CTF players to get started with the field of Memory Forensics.

### Challenge Description

> One of the clients of our company, lost the access to his system due to an unknown error. He is supposedly a very popular "environmental" activist. As a part of the investigation, he told us that his go to applications are browsers, his password managers etc. We hope that you can dig into this memory dump and find his important stuff and give it back to us.

- **Note :** This challenge is composed of 3 flags.

# Solution

### Image Identification 

- When a **Memory dump** is taken, it is extremely important to know the information about the **operating system** that was in use.
- **Volatility** will try to read the image and suggest the related profiles for the given memory dump. 
- The `imageinfo` plugin is used to identify the **Operating System, service pack, and hardware architecture (32 or 64 bit) DTB address, time the sample was collected** etc.

```
/mnt/e/Tools/V.exe -f "Lab 2.raw" imageinfo
```

![](https://github.com/a3X3k/MemLabs/blob/main/Lab%202/Assets/1.png)

### Analysis

- From the challenge description **"environmental" activist** we shall understand that its indirectly meant for the **environmental variables**.
- In **Volatility** `envars` plugin is used to display a **process's environment variables**. 

```
/mnt/e/Tools/V.exe -f "Lab 2.raw" --profile=Win7SP1x64 envars
```

![](https://github.com/a3X3k/MemLabs/blob/main/Lab%202/Assets/2.png)

- We shall see that there is a `Base64` encoded text given as **environmental variables**.

```
ZmxhZ3t3M2xjMG0zX1QwXyRUNGczXyFfT2ZfTDRCXzJ9
```

- [`Decrypting`](https://www.base64decode.org/) it gives the **first** flag.

```
flag{w3lc0m3_T0_$T4g3_!_Of_L4B_2}
```

- Now let's run the `pslist` plugin which lists all **high-level** running processes.

```
/mnt/e/Tools/V.exe -f "Lab 2.raw" --profile=Win7SP1x64 pslist
```

![](https://github.com/a3X3k/MemLabs/blob/main/Lab%202/Assets/3.png)

![](https://github.com/a3X3k/MemLabs/blob/main/Lab%202/Assets/4.png)

- We shall see that the `Nodepad.exe` is running.
- So let's use the `cmdline` plugin which lists all the **command line arguments** with the help of PID.

```
/mnt/e/Tools/V.exe -f "Lab 2.raw" --profile=Win7SP1x64 cmdline
```

![](https://github.com/a3X3k/MemLabs/blob/main/Lab%202/Assets/5.png)

![](https://github.com/a3X3k/MemLabs/blob/main/Lab%202/Assets/6.png)

- It's clear that there is a file named `Hidden.kdbx` in secrets directory.

#### Kdbx

> The data files created by **KeePass Password Safe** are known as **KDBX** files.
> They usually refer to the **KeePass Password Database**. 
> These files contain passwords in an encrypted database wherein they can only be viewed if the user set a master password and accessed them through that master password.

- Let's extract the **kdbx** file.
- `dumpfiles` plugin is used to **extract** the memory mapped and cached files.
- Inorder to `dump` the file we must know the **physical address** of the file ( ie ) the location of the file.
- `filescan` plugin is used to find **FILE_OBJECTs** in physical memory.
- Since we know the file which is required we shall grep it directly by specifying the file name.

```
/mnt/e/Tools/V.exe -f "Lab 2.raw" --profile=Win7SP1x64 filescan | grep Hidden
```

![](https://github.com/a3X3k/MemLabs/blob/main/Lab%202/Assets/7.png)

- Now we got the physical address of the file and so that we shall dump the file using the physical address as offset.

```
/mnt/e/Tools/V.exe -f "Lab 2.raw" --profile=Win7SP1x64 dumpfiles -Q 0x000000003fb112a0 -D .
```

![](https://github.com/a3X3k/MemLabs/blob/main/Lab%202/Assets/8.png)

![](https://github.com/a3X3k/MemLabs/blob/main/Lab%202/Assets/12.png)

- Now we got the [`KDBX`](https://github.com/a3X3k/MemLabs/blob/main/Lab%202/Assets/1.kdbx) file and when we try to open, it asks for the password and we don't have any password now.
- So now we have to find the password.
- The [`Cheat Sheet`](https://www.codersnoon.com/2021/01/volatility-cheatsheet-memory-forensics.html) which I used to learn the volatility plugin gave me the hint what to do.

![](https://github.com/a3X3k/MemLabs/blob/main/Lab%202/Assets/20.png)

- As you can see, just grepping for keywords like `kdbx`,`Important`,`Pass` or `Password` might give you something since in most of the cases the file name will be kept unique to identify it.
- This entirely depends on the challenge. 

```
/mnt/e/Tools/V.exe -f "Lab 2.raw" --profile=Win7SP1x64 filescan | grep -i "Pass"
```

![](https://github.com/a3X3k/MemLabs/blob/main/Lab%202/Assets/9.png)

- But luckily in this challenge, it was named like that.
- We shall see that there is a file named **Password.png**.  
- Let's dump the file now.
- `dumpfiles` plugin is used to extract the memory mapped and cached files.

```
/mnt/e/Tools/V.exe -f "Lab 2.raw" --profile=Win7SP1x64 dumpfiles -Q 0x000000003fce1c70 -D .
```

![](https://github.com/a3X3k/MemLabs/blob/main/Lab%202/Assets/10.png)

- Now we got the [`Image`](https://github.com/a3X3k/MemLabs/blob/main/Lab%202/Assets/11.png) file.

![](https://github.com/a3X3k/MemLabs/blob/main/Lab%202/Assets/11.png)

- We got the password now.

```
P4SSw0rd_123
```

- Opening the **kdbx** file using **KeePass Password Database** software, gives some `usernames` and `passwords`.

![](https://github.com/a3X3k/MemLabs/blob/main/Lab%202/Assets/21.png)

- But all of them are random usernames and passwords. 

![](https://github.com/a3X3k/MemLabs/blob/main/Lab%202/Assets/22.png)

- Finally under **Recycle Bin**, we shall see that there is a username called `Flag`.
- Opening it's corresponding Password gives the second flag.

```
flag{w0w_th1s_1s_Th3_SeC0nD_ST4g3_!!}
```

- Still we have one more flag to find. 
- From the hint in the challenge description **applications are browsers**, we shall understand that there is something to do with the browser **history/cookies/Downloads** etc.
- Trying with the `iehistory` plugin doesn't give anything.
- If we go back to the `pslist` view, we shall see the **Chrome Browser** running.
- So it's clear that the **browser** associated to him is **Chrome**. 

![](https://github.com/a3X3k/MemLabs/blob/main/Lab%202/Assets/14.png)

- Now we have to extract all useful information from Google Chrome's address space.
- By default volatility doesn't have the plugins to view/extract the chrome history.
- But it can be additionally installed.
- You can refer this [`Blog`](http://blog.superponible.com/2014/08/31/volatility-plugin-chrome-history/).
- You can pull the chrome history [`manually`](https://www.foxtonforensics.com/browser-history-examiner/chrome-history-location#:~:text=Chrome%20Searches%20are%20stored%20in,within%20the%20%27urls%27%20table.&text=Chrome%20Session%20Data%20is%20stored,and%20%27Last%20Tabs%27%20files) without plugins.
- `C:\Users\<username>\AppData\Local\Google\Chrome\User Data\Default` this is where the chrome history is stored in `Windows 7`.
- We are concerned about `Windows 7` here since our profile is `Windows 7`.
- Just grepping `Chrome\User Data\Default` or `Chrome\Default\History` gives the **Web Data SQLite Database**.

```
/mnt/e/Tools/V.exe -f "Lab 2.raw" --profile=Win7SP1x64 filescan | grep Chrome | grep Default | grep History
```

![](https://github.com/a3X3k/MemLabs/blob/main/Lab%202/Assets/15.png)

- Let's dump the file now using the `dumpfiles` plugin.

```
/mnt/e/Tools/V.exe -f "Lab 2.raw" --profile=Win7SP1x64 dumpfiles -Q 0x000000003fcfb1d0 -D .
```

![](https://github.com/a3X3k/MemLabs/blob/main/Lab%202/Assets/16.png)

- Opening it with **DB Browser** displays all the entries in the database.

![](https://github.com/a3X3k/MemLabs/blob/main/Lab%202/Assets/17.png)

- We shall see that there is a [`Mega Link`](https://mega.nz/#F!TrgSQQTS!H0ZrUzF0B-ZKNM3y9E76lg)
- Opening it gives the `ZIP` file.
- But its encrypted and looking the hex dump as we did in [`Lab 1`](https://github.com/a3X3k/MemLabs/blob/main/Lab%201/readme.md) we shall get the hint for the password.

![](https://github.com/a3X3k/MemLabs/blob/main/Lab%202/Assets/18.png)

#### Hint

> Password is SHA1(stage-3-FLAG) from Lab-1. Password is in lowercase

- We already know the stage 3 password of Lab 1.
- So finding the SHA1 hash of it gives the password.

#### Stage 3 Password of Lab 1 

```
flag{w3ll_3rd_stage_was_easy}
```

![](https://github.com/a3X3k/MemLabs/blob/main/Lab%202/Assets/19.png)

```
6045dd90029719a039fd2d2ebcca718439dd100a
```

- Now after unzipping it we get a [`Image`](https://github.com/a3X3k/MemLabs/blob/main/Lab%202/Assets/Important.png) file which has the flag. 

```
flag{oK_So_Now_St4g3_3_is_DoNE!!}
```

### Flags

```
flag{w3lc0m3_T0_$T4g3_!_Of_L4B_2}
flag{w0w_th1s_1s_Th3_SeC0nD_ST4g3_!!}
flag{oK_So_Now_St4g3_3_is_DoNE!!}
```

### Volatility Commands Reference - [`📖`](https://github.com/volatilityfoundation/volatility/wiki/Command-Reference#dumpfiles) [`📖`](https://www.codersnoon.com/2021/01/volatility-cheatsheet-memory-forensics.html) 











