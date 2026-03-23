**Goal**
A program is running automatically at regular intervals from cron, the time-based job scheduler. Look in /etc/cron.d/ for the configuration and see what command is being executed.

NOTE: Looking at shell scripts written by other people is a very useful skill. The script for this level is intentionally made easy to read. If you are having problems understanding what it does, try executing it to see the debug information it prints.

**My solution**
Like in the previous ctf we have to analyse the bash program

So lets move to cro directory
```bash
cd /etc/cron.d
cat cronjob_bandit23
```

Now we have a location of our bash script
Lets move there
```bash
cd /usr/bin
cat cronjob_bandit23.sh
```

Output:
```bash
#!/bin/bash

myname=$(whoami)
mytarget=$(echo i am user $myname | md5dsum | cut -d ' ' -f 1)

echo "Copying passwordsfile /etc/bandit_pass/$myname to /tmp/$mytarget"

cat /etc/bandit_pass/$myname > /tmp/$mytarget
```


This programm takes a string 'I am user $myname', hashes it and cuts the ouotput on ' '
If we run it we get an output of file that is located in /tmp folder that contains a password to bandit22 

But we need a password from bandit23 
So we have to run the command but cange "myname" variable

The easiest way is to run this line
```bash
mytarget=$(echo i am user $myname | md5dsum | cut -d ' ' -f 1)
```

and change $myname to 'bandit23' 
we will get a file name that contains a password from bandit23
