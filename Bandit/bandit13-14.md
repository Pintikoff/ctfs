The password for the next level is stored in /etc/bandit_pass/bandit14 and can only be read by
user bandit14. For this level, you don’t get the next password, but you get a private SSH key
that can be used to log into the next level. Look at the commands that logged you into previous
bandit levels, and find out how to use the key for this level.

We need to connect to a bandit 14 server via ssh key to do that we have to first copy the 
ssh key to our mashine(VM). On of many ways to do that is doing that via scp (OpenSSH secure file copy).

At first we have to return to out mashine 

```bash
exit
```

Now lets copy out sshkey.private file 

```bash
scp -P 2220 bandit13@badnit.labs.overthewire.org:/home/bandit13/sshkey.private .
```

'.' refers to our current directory.

After that we will be asked to enter a password to BANDIT13. So enter it.

After that we have to check that only the owner of sshkey.private is able to read/write it.

```bash
ls -al 
```

If someone except owner is able to overwrite/read the file: use chmod commands

```
chmod 600
```

After a SSH-Key setup, we can use it to connect to bandit14 level

```
ssh -i sshkey.private bandit14@bandit.labs.overthewire.org -p 2220
```

DONE.
