# <div align="center">[Dev Diaries](https://tryhackme.com/room/devdiaries)</div>
<div align="center">OSINT</div>
<div align="center">
  <img height="200" alt="image" src="https://github.com/user-attachments/assets/c3220cc6-add4-46df-9012-2dc9a17b45c6" />
</div>

# Challange
We have just launched a website developed by a freelance developer. The source code was not shared with us, and the developer has since disappeared without handing it over.

Despite this, traces of the development process and earlier versions of the website may still exist online.

You are only given the website's primary domain as a starting point: marvenly.com

<br>

> Q1. What is the subdomain where the development version of the website is hosted?

To identify subdomains, I used DNS enumeration via [DNSDumpster](https://dnsdumpster.com/).

It gave me two subdomain :
- `admin.marvenly.com`
- `uat-testing.marvenly.com`

Answer:
``` 
uat-testing.marvenly.com
```
<br>

> Q2. What is the GitHub username of the developer?

Upon visiting the sub domain: `uat-testing.marvenly.com`, I found a footer credit:

`Website developed by notvibecoder23`

I searched this username on GitHub and confirmed the developer’s profile and repository

Answer:
```
notvibecoder23
```

<br>

> Q3. What is the developer's email address?

For this question I googled `How to find Email address from github username`, I then performed the follwoing steps:
1. Opened the repository marvenly_site
2. Navigated to a commit
3. Appended .patch to the commit URL 
`https://github.com/notvibecoder23/marvenly_site/commit/7a7090dd0ce6b8932d0c4a44e050e7fa1e0b2edd.patch`

This revealed: 
```text
From 7a7090dd0ce6b8932d0c4a44e050e7fa1e0b2edd Mon Sep 17 00:00:00 2001
From: notvibecoder23 <freelancedevbycoder23@gmail.com>
Date: Tue, 20 Jan 2026 00:38:53 +0800
Subject: [PATCH] Parking the domain until the issue is solved

---
 index.html | 2 +-
 1 file changed, 1 insertion(+), 1 deletion(-)

diff --git a/index.html b/index.html
index dd41b6f..f150301 100644
--- a/index.html
+++ b/index.html
@@ -1,5 +1,5 @@
 <html>
 <body>
-UNDER CONSTRUCTION
+DOMAIN FOR SALE
 </body>
 </html>
```

Answer:
```
freelancedevbycoder23@gmail.com
```

<br>

> Q4. What reason did the developer mention in the commit history for removing the source code?

Reviewing the commits made by the user showed: 4 commits, one of which gave me the answer for this question.

Answer:
```
The project was marked as abandoned due to a payment dispute
```

<br>

> Q5. What is the value of the hidden flag?

Inspecting the commit further revealed a hidden comment towards the end:
`<!-- removed the signature, but I'm leaving something as my hidden signature THM{g1t_h1st0ry_n3v3r_f0rg3ts} -->`

Answer:
```
THM{g1t_h1st0ry_n3v3r_f0rg3ts}
```


## 🧠 Summary
- subdomain enumeration performed using DNSDumpster
- identified development environment at uat-testing.marvenly.com
- developer username discovered via website footer
- GitHub profile located and repository analyzed
- commit data extracted using .patch method
- developer email identified from commit metadata
- commit history reviewed to find reason for project removal
- hidden flag discovered within commit comments

<br>

## 🔑 Takeaway
- easy OSINT challenge but shows how powerful basic recon can be
- demonstrates how developers can unintentionally leak sensitive info through GitHub and subdomains
- reinforced the importance of checking public data sources during investigations
  
