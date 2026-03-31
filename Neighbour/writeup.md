# <div align="center">[Neighbour](https://tryhackme.com/room/neighbour)</div>
<div align="center">Insecure Direct Object Reference(IDOR)</div>
<br>
<div align="center">
  <img height="200" alt="image" src="https://github.com/user-attachments/assets/6150c48a-6752-4b42-ae99-fcd31dc81f33" />
</div>

# Task
Check out our new cloud service, Authentication Anywhere -- log in from anywhere you would like! Users can enter their username and password, for a totally secure login process! You definitely wouldn't be able to find any secrets that other people have in their profile, right?

> Find the flag on your neighbor's logged in page!

Navigated to the link ` http://10.48.132.180 `

This opened a login page displaying the hint:

` Don't have an account? Use the guest account! (Ctrl+U) `

Using `Ctrl + U`, I viewed the page source and found the following comment:

`<!-- use guest:guest credentials until registration is fixed. "admin" user account is off limits!!!!! -->`

This revealed valid credentials:

- Username: guest
- Password: guest

After logging in with the guest account, the application displayed:

` Hi, guest. Welcome to our site. Try not to peep your neighbor's profile. `

I noticed that the URL at this point was:

` http://10.48.132.180/profile.php?user=guest `

The user parameter in the URL appeared to directly control which profile was being accessed.
This indicates a potential Insecure Direct Object Reference (IDOR) vulnerability, where user-controlled input is used without proper authorization checks.
To test this, I modified the URL parameter to :

`http://10.48.132.180/profile.php?user=admin`

After changing the parameter, the page displayed:

` Hi, admin. Welcome to your site. The flag is: flag{66be95c478473d91a5358f2440c7af1f} `


Answer:
```
flag{66be95c478473d91a5358f2440c7af1f}
```
