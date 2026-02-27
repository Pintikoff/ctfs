*Level 15-16*

**Level Goal:

The password for the next level can be retrieved by submitting the password of the current level to port 30001 on localhost using SSL/TLS encryption.

Helpful note: Getting “DONE”, “RENEGOTIATING” or “KEYUPDATE”? Read the “CONNECTED COMMANDS” section in the manpage.

**My solution:

This task was really easy as i already tried getting a password this way on the last challange

***Step 1***
Right away when i saw SSL/TLS encryption, i knew that i have to establish connection via openssl s_client command

```bash
openssl s_client -connect localhost:3001
```
DONE!

After that i simply put the password in and got a key to my next ssh connection

