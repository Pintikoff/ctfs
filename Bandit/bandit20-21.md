**Level Goal**
There is a setuid binary in the homedirectory that does the following: it makes a connection to localhost on the port you specify as a commandline argument. It then reads a line of text from the connection and compares it to the password in the previous level (bandit20). If the password is correct, it will transmit the password for the next level (bandit21).

**My solution** 
We basically have to listen for a port connection sing nc and then send level20 password so the binary give us a password
for nexxt level

```terminal2 bash
nc -l 1234
```

```terminal1 bash
./suconnect 1234
```

```terminal1 bash
//send a password from level20
```

Done
