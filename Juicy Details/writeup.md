# <div align="center">[Juicy Details](https://tryhackme.com/room/juicydetails)</div>
<div align="center">Log Analysis</div>
<br>
<div align="center">
  <img height="200" alt="image" src="https://github.com/user-attachments/assets/fbbe9567-4a42-4169-80d3-b92db04d431e" />
</div>

# Task 1. Introduction
You were hired as a SOC Analyst for one of the biggest Juice Shops in the world and an attacker has made their way into your network. 

Your tasks are:

- Figure out what techniques and tools the attacker used
- What endpoints were vulnerable
- What sensitive data was accessed and stolen from the environment

An IT team has sent you a zip file containing logs from the server. Download the attached file, type in "I am ready!" and get to work! There's no time to lose!
> Are you ready?
```
I am ready!
```

# Task 2. Reconnaissance
Analyze the provided log files.

Look carefully at:
- What tools the attacker used
- What endpoints the attacker tried to exploit
- What endpoints were vulnerable

> Q1. What tools did the attacker use? (Order by the occurrence in the log)


Started by analyzing the `access.log` file, which contains web server access logs.

To identify tools used by the attacker, I searched for known tool signatures within the logs.

`
grep -i "nmap\|sqlmap\|hydra\|curl\|feroxbuster" access.log
`


Observed multiple tools in the logs including: nmap, hydra, sqlmap, curl and feroxbuster.
These tools suggest different phases of the attack, including reconnaissance, brute-force attempts, injection testing, and directory enumeration.


Answer:

```
nmap, hydra, sqlmap, curl, feroxbuster
```

<br>


> Q2. What endpoint was vulnerable to a brute-force attack?

Hydra is commonly used for brute-force attacks, so I searched for its activity within the logs.

``
grep -i "hydra" access.log
``

From the results, repeated requests were observed targeting the endpoint: `/rest/user/login`

This pattern indicates a brute-force attempt against the login functionality.

Answer:
```
/rest/user/login
```
<br> 


> Q3. What endpoint was vulnerable to SQL injection?


sqlmap is generally used for SQL Injection, so I searched for its activities withing the logs.
`
grep -i "sqlmap" access.log
`


The first entry itself showed that the endpoint, /rest/products/search was attacked with a SQL map for SQL injection vulnerability.

Answer:
```
/rest/products/search
```
<br> 


> Q4. What parameter was used for the SQL injection?


After searching for sqlmap from the previous question, I noticed that the endpoint used was `/rest/products/search?q=` followed by the injected SQL queries
so the parameter used was 'q'

Answer:
```
q
```
<br> 


> Q5. What endpoint did the attacker try to use to retrieve files? (Include the /)


To identify file-related access, I searched for endpoints related to file retrieval.

`
grep -i "ftp" access.log
`

The logs showed requests targeting the following endpoint: `/ftp`

This indicates the attacker was attempting to access files through this route.  
The activity was also associated with enumeration tools like feroxbuster.

Answer:
```
/ftp
```

# Task 3. Stolen data
Analyze the provided log files.

Look carefully at:

- The attacker's movement on the website
- Response codes
- Abnormal query strings

> Q1. What section of the website did the attacker use to scrape user email addresses?

This one was a bit tricky. I had to think about places where the usernames might be visible to the public, like the reviews section. 

To confirm if that section existed I used:
`
grep -i "review" access.log
`
This gave me the endpoint: `/rest/products/1/reviews` which told me that the adversary used the product review section to scrape user emails.

Answer:
```
product reviews
```
<br> 


> Q2. Was their brute-force attack successful? If so, what is the timestamp of the successful login? (Yay/Nay, 11/Apr/2021:09:xx:xx +0000)


The brute-force attack was performed by hydra so I went back and checked hydra related activity within the logs with the command I gave above in Task2 Q2.
After a few tries I finally found one successful login attempt:

`::ffff:192.168.10.5 - - [11/Apr/2021:09:16:31 +0000] "POST /rest/user/login HTTP/1.0" 200 831 "-" "Mozilla/5.0 (Hydra)" `

Answer:
```
Yay, 11/Apr/2021:09:16:31 +0000
```
<br> 


> Q3. What user information was the attacker able to retrieve from the endpoint vulnerable to SQL injection?

From previous analysis, the product review endpoint exposed user email addresses.

The SQL injection attack on `/rest/products/search` indicates that the attacker was able to extract additional data from the database.

SQL injection typically allows access to sensitive fields such as credentials, including passwords.

Combining these observations, the attacker was able to retrieve the user emails and passwords
Answer:
```
email, password
```
<br> 


> Q4. What files did they try to download from the vulnerable endpoint? (endpoint from the previous task, question #5)


The endpoint referred to over here is /ftp 
Looking at the `vsftpd.log` file, which contains FTP server logs, two successful download entries were observed:


`Sun Apr 11 09:35:45 2021 [pid 8154] [ftp] OK DOWNLOAD: Client "::ffff:192.168.10.5", "/www-data.bak", 2602 bytes, 544.81Kbyte/sec`


`Sun Apr 11 09:36:08 2021 [pid 8154] [ftp] OK DOWNLOAD: Client "::ffff:192.168.10.5", "/coupons_2013.md.bak", 131 bytes, 3.01Kbyte/sec`

Answer: 
```
coupons_2013.md.bak, www-data.bak
```
<br> 

> Q5. What service and account name were used to retrieve files from the previous question? (service, username)

As seen in the previous question, the service used is ftp 
checking the logs in `vsftpd.log` file we see three login fail attempts from the user `anonymous` but after the thirst attempt we see the log entry:

`Sun Apr 11 08:15:55 2021 [pid 6529] CONNECT: Client "::ffff:127.0.0.1" `


`Sun Apr 11 08:15:58 2021 [pid 6526] [ftp] OK LOGIN: Client "::ffff:127.0.0.1", anon password "?"`

which suggests that the ftp login was successful

Answer:
```
ftp, anonymous
```

<br>

> Q6. What service and username were used to gain shell access to the server? (service, username)


Looking at the `auth.log` file which store the authentication logs, I used the following command to check for the logs where the password was Accepted:

` grep -i "Accepted" auth.log `


This gave the following logs as output:

`Apr 11 09:41:19 thunt sshd[8260]: Accepted password for www-data from 192.168.10.5 port 40112 ssh2`


`Apr 11 09:41:32 thunt sshd[8494]: Accepted password for www-data from 192.168.10.5 port 40114 ssh2 `

These logs show that the attacker successfully authenticated via the SSH service using the `www-data` account, gaining shell access to the system.

Answer:
```
ssh, www-data
```
<br>


## 🧠 Summary

- reconnaissance performed using nmap  
- brute-force attack using hydra on `/rest/user/login`  
- SQL injection attempted using sqlmap on `/rest/products/search`  
- directory enumeration using feroxbuster  
- attacker accessed `/ftp` endpoint  
- files downloaded via anonymous FTP login  
- SSH access gained using `www-data` account  
