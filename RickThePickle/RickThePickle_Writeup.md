# Rick the Pickle — Write-up

## 1. Web Enumeration

I enumerated the website and identified several useful paths:

- **`robots.txt`** — contained the text `Wubbalubbadubdub`.
- **`assets/`** — contained miscellaneous GIFs.
- **`login.php`** — presented a login page.

The login page source provided the following credentials:

```text
Username: R1ckRul3s
Password: Wubbalubbadubdub
```

## 2. Command Execution

After logging in, I used the command-execution functionality to inspect the current directory:

```bash
ls -al
```

The listing included these notable files:

```text
Sup3rS3cretPickl3Ingred.txt
clue.txt
denied.php
index.html
login.php
portal.php
robots.txt
```

The files `Sup3rS3cretPickl3Ingred.txt` and `clue.txt` were particularly relevant.

- **`Sup3rS3cretPickl3Ingred.txt`** contained the first ingredient: `mr. meeseek hair`.
- **`clue.txt`** said: `Look around the file system for the other ingredient.`

## 3. Inspecting the Page Source

The page source contained a Base64-encoded string:

```text
Vm1wR1UxTnRWa2RUV0d4VFlrZFNjRlV3V2t0alJsWnlWbXQwVkUxV1duaFZNakExVkcxS1NHVkliRmhoTVhCb1ZsWmFWMVpWTVVWaGVqQT0==
```

Decoding the string revealed the text:

```text
rabbit hole
```

This suggested that I should continue exploring the file system.

## 4. Finding the Remaining Ingredients

I inspected Rick's home directory:

```bash
ls -la /home/rick
```

The second ingredient was located in a file named `second ingredients` in `/home/rick`. I read it with:

```bash
less "/home/rick/second ingredients"
```

The third ingredient was located in `/root/3rd.txt`:

```bash
less /root/3rd.txt
```

## Summary

The steps documented in this challenge were:

1. Enumerated the website and found `robots.txt`, `assets/`, and `login.php`.
2. Retrieved the login credentials from the page source.
3. Used the command-execution feature to inspect files on the server.
4. Read the first ingredient and clue.
5. Decoded the Base64 string in the page source.
6. Located the second ingredient in Rick's home directory and the third in `/root/3rd.txt`.

*This write-up organizes the commands and findings recorded during the lab. The original notes did not include the contents of the second and third ingredient files.*
