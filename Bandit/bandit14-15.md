*Level 14-15*

**Level Goal:

The password for the next level can be retrieved by submitting the password of the current level to port 30000 on localhost.

**My solution:

After reading documentations about commands given to me like nmap, nc, openssl and s_client.

I decided to try to connect to the localhost via ssh, which doesn't event make sense.

***Step 1***
After failure i realised, that it would be better to scnan myself(localhost) to discover what ports and services are open.

```bash
nmap localhost
```

The output gave me some information:
For example, that service on port 30000 was actually ndmps. It's a protocol used to transport data between network attached devices.

***Step 2***

After discovering that i used wrong commands (ssh, openssl s_client and even scp) i realised that i have to establish tcp connection
first.

Knowing that i used this command:
```bash
nc localhost 30000
```

After that I entered a password that was stored in its default location(/etc/bandit_pass/bandit14).

**Done!**

I recieved a key for the next level...
