**Level goal**
The password for the next level is stored in a file readme in the homedirectory. Unfortunately, someone has modified .bashrc to log you out when you log in with SSH.

**My solution**
This level wasn't that hard either.
The only thing that i had to do is open a man page for SSH and look for something that lets us open a SSH connection on background.

After searching for a bit I found this argument

```bash 
ssh -f
```

Now lets combine this command with our default ssh command

```bash 
ssh bandit18@bandit.labs.overthewire.org -p 2200 -f cat readme
```

**Explanation:** 
```bash
-f cat readme
```
SSH connects to the server.

It runs the remote command cat readme.

Because a command is provided, SSH does not start an interactive shell.

cat readme prints the file contents.

The SSH session ends after the command finishes.

- The -f option just tells OpenSSH ssh to fork to the background after authentication.

DONE!
