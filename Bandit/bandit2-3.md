**Level Goal:**

The password for the next level is stored in a file called --spaces in this filename-- located in the home directory

**My Solution**
```bash
cat -- "--spaces in this filename--"
```

The first -- tells cat to stop processing options and treat everything after it as a filename.
The quotation marks ensure that the entire string (including spaces) is treated as one single argument.

This allows cat to correctly read the file and display its contents.

DONE!
I reccieved a key
