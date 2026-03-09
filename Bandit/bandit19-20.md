**Level Goal**

To gain access to the next level, you should use the setuid binary in the homedirectory.
Execute it without arguments to find out how to use it. 
The password for this level can be found in the usual place (/etc/bandit_pass), 
after you have used the setuid binary.

**My solution**

As said in level goal, i executed the file
```bash
./bandit20-do
```

This executable gave me following output
```output
Run a command as another user.
  Exmaple: ./bandit20-do whoami
```

After running the command from example you can see that you executed the command as bandit20 user.
```bash
./bandit20-do whoami
```
```output
bandit20
```

So now you can just cat the password from /etc/bandit_pass/bandit20
```
./bandit20-do cat /etc/bandit_pass/bandit20
```

DONE!
We got the password
