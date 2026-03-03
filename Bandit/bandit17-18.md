**Level Goal**
There are 2 files in the homedirectory: passwords.old and passwords.new. The password for the next level is in passwords.new and is the
only line that has been changed between passwords.old and passwords.new.

***Comments***
This is probably one of the easiest levels out there.

**My Solution**
We basically have to compare two text files.
Therefor we use "diff" command with.
```bash
diff passwords.old passwords.new
```
Now we have a few lines that were changed.
Try them out to find the right password.

That's it!
Now you have a password for 18th level.
