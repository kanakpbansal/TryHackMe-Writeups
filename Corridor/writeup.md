# <div align="center">[Corridor](https://tryhackme.com/room/corridor)</div>
<div align="center">Insecure Direct Object References(IDOR)</div>
<br>
<div align="center">
  <img  height="200" alt="image" src="https://github.com/user-attachments/assets/a4087086-1a93-4903-aefc-44d323af6906" />
</div>

# Task 1. Challenge

You have found yourself in a strange corridor. Can you find your way back to where you came?

In this challenge, you will explore potential IDOR vulnerabilities. Examine the URL endpoints you access as you navigate the website and note the hexadecimal 
values you find (they look an awful lot like a hash, don't they?). This could help you uncover website locations you were not expected to access.

> What is the flag?

After launching the target machine, I visited: `http://10.48.174.185`
The page displayed a corridor image with multiple clickable doors.
Clicking any door opened a room — but all rooms appeared empty.

When clicking on different doors, I noticed that:

- The URL contained hexadecimal strings
- These values looked like hashes

Example: `http://10.48.174.185/c4ca4238a0b923820dcc509a6f75849b`

Inspecting the page source revealed all door endpoints: 

```
<! DOCTYPE html>
html lang="en">
head
<meta charset="utf-8">
<meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
link rel="stylesheet" href="https://stackpath. bootstrapcdn. com/bootstrap/4.5.0/css/bootstrap.min.cs
integrity="sha384-9aIt2nRpC12Uk9gS9baDl411NQApFmC26EwAOH8WgZL5MYYxFfc+NcPb1dKGj7Sk" crossorigin="anonymous">
<title>Corridor</title>
<link rel="stylesheet" href="/static/css/main. css">
</head>
<body>

<img src="/static/img/corridor.png" usemap="#image-map">
<map name="image-map">
<area target="" alt="c4ca4238a0b923820dcc509a6f75849b" title="c4ca4238a0b923820dcc509a6f75849b" href="c4ca4238a0b923820dcc509a6f75849b" coords="257, 893, 258, 332, 325, 351, 325, 860" shape="poly">
<area target="" alt="c81e728d9d4c2f636f067f89cc14862c" title="c81e728d9d4c2f636f067f89cc14862c" href="c81e728d9d4c2f636f067f89cc14862c" coords="469, 766, 503, 747, 501, 405, 474, 394" shape="poly">
<area target="" alt="eccbc87e4b5ce2fe28308fd9f2a7baf3" title="eccbc87e4b5ce2fe28308fd9f2a7baf3" href="eccbc87e4b5ce2fe28308fd9f2a7baf3" coords="585, 698, 598, 691, 593, 429, 584, 421" shape="poly">
<area target="" alt="a87ff679a2f3e71d9181a67b7542122c" title="a87ff679a2f3e71d9181a67b7542122c" href="a87ff679a2f3e71d9181a67b7542122c" coords="650, 658, 644, 437, 658, 652, 655, 437" shape="poly">
<area target="" alt="e4da3b7fbbce2345d7772b0674a318d5" title="e4da3b7fbbce2345d7772b0674a318d5" href="e4da3b7fbbce2345d7772b0674a318d5" coords="692, 637, 690, 455, 695, 628, 695, 467" shape="poly">
<area target="" alt="1679091c5a880faf6fb5e6087eb1b2dc" title="1679091c5a880faf6fb5e6087eb1b2dc" href="1679091c5a880faf6fb5e6087eb1b2dc" coords="719, 620, 719, 458, 728, 471, 728, 609" shape="poly">
<area target="" alt="8f14e45fceea167a5a36dedd4bea2543" title="8f14e45fceea167a5a36dedd4bea2543" href="8f14e45fceea167a5a36dedd4bea2543" coords="857, 612, 933, 610, 936, 456, 852, 455" shape="poly">
<area target="" alt="c9f0f895fb98ab9159f51fd0297e236d" title="c9f0f895fb98ab9159f51fd0297e236d" href="c9f0f895fb98ab9159f51fd0297e236d" coords="1475, 857, 1473, 354, 1537, 335, 1541, 901" shape="poly">
<area target="" alt="45c48cce2e2d7fbdealafc51c7c6ad26" title="45c48cce2e2d7fbdealafc51c7c6ad26" href="45c48cce2e2d7fbdealafc51c7c6ad26" coords="1324, 766, 1300, 752, 1303, 401, 1325, 397" shape="poly">
<area target="" alt="d3d9446802a44259755d38e6d163e820" title="d3d9446802a44259755d38e6d163e820" href="d3d9446802a44259755d38e6d163e820" coords="1202, 695, 1217, 704, 1222, 423, 1203, 423" shape="poly">
<area target="" alt="6512bd43d9caa6e02c990b0a82652dca" title="6512bd43d9caa6e02c990b0a82652dca" href="6512bd43d9caa6e02c990b0a82652dca" coords="1154, 668, 1146, 661, 1144, 442, 1157, 442" shape="poly">
<area target="" alt="c20ad4d76fe97759aa27a0c99bff6710" title="c20ad4d76fe97759aa27a0c99bff6710" href="c20ad4d76fe97759aa27a0c99bff6710" coords="1105, 628, 1116, 633, 1113, 447, 1102, 447" shape="poly">
<area target="" alt="c51ce410c124a10e0db5e4b97fc2af39" title="c51ce410c124a10e0db5e4b97fc2af39" href="c51ce410c124a10e0db5e4b97fc2af39" coords="1073, 609, 1081, 620, 1082, 459, 1073, 463" shape="poly">
</map>
</body
</html>
```

This confirmed that:
- Each door corresponds to a hash value
- These hashes are directly exposed in the frontend

Using an online tool (`https://www.dcode.fr/ `) I decoded the hashes.

They were identified as MD5 hash values.

Decoded results:
```
c4ca4238a0b923820dcc509a6f75849b → 1
c81e728d9d4c2f636f067f89cc14862c → 2
...
```

I recognised a patter : 
`Each room = MD5(number) for numbers 1 to 13`

Since the application directly maps: 
`/<md5(number)> → room`

We can try accessing values outside the intended range.

So I encoded 14 in md5 hash and tried : ` http://10.48.174.185/aab3238922bcc25a6f606eb525ffdc56 ` It again gave me empty room so I tried encoding 0 : ` http://10.48.174.185/897316929176464ebc9ad085f31e7284 `. 
It gave me the flag.

Answer : 

```
flag{2477ef02448ad9156661ac40a6b8862e}
```


<br>

# 🧠 Summary
- Identified hashed values in URLs while navigating doors
- Found all endpoints in page source
- Determined hashes were MD5 of sequential numbers (1–13)
- Tested out-of-range values
- Accessed hidden endpoint using MD5(0)
- Successfully retrieved the flag

<br>

# Visual Walkthrough

A visual version of this challenge (with screenshots) is available on Medium:  
[View on Medium](https://medium.com/@kanakpbansal/corridor-walkthrough-tryhackme-cd048e73054d)
