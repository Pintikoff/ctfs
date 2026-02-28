**Level Goal**
The credentials for the next level can be retrieved by submitting the password of the current level to a port on localhost in the range
31000 to 32000. First find out which of these ports have a server listening on them. Then find out which of those speak SSL/TLS and which
don’t. There is only 1 server that will give the next credentials, the others will simply send back to you whatever you send to it.

**My Solution**
There are multiple ways to solve this CTF and I will show two of them.

**First Solution:**
This solution requires only a single nmap command
```bash
nmap -A localhost -p 31000-3200
```
This nmap scan - scans every port in range of 31000 and 32000 aggressively.
It basically means that nmap will send service probes (probe requests) to determine services and protocols.
So if a port asks for input, nmap will also send a probe and analyse its answer to get info about protocol the port is using.

After command successfully ran and gave us an output, we have to analyse it. 
If you did everything correctly, in one of the lines, you will see something like this:
```
(port)/tcp open ssl/unknown
  ...
    ...
      Wrong! Please enter the correct current password.
```
This is a response port sent us as an answer to nmap's probe.

now use a port it that gave this output to send a password from **current** level.


**Second Solution:**
You can use ss command to scan ports and filter it using grep to output only targeted ports
```bash
ss -tl | grep -E ':(31[0-9]{3} | 32000)'
```
-tl basiccally outputs listening ports that use tcp protocol.


**Final Step**

now just try submitting a password via netcat

```bash
ncat --ssl localhost:(port)
```

And enter our password.

After that you will become a shh key. 

Exit from server and copy the key to your PC

(Don't forge to set right permission like 600)
```
chmod 600 (file with key)
```

Now simply connect via ssh using it
```bash
ssh -i private.key bandit17@bandit.labs.overthewire.org -p 2220
```

Done!
I'm tired.
