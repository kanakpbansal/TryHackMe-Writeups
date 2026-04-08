# <div align="center">[Have A Break](https://tryhackme.com/room/haveabreak)</div>
<div align="center">Digital Forensics + OSINT</div>
<br>
<div align="center">
  <img height="200" alt="image" src="https://github.com/user-attachments/assets/591de30d-842e-44ae-897b-513a416683df" />

</div>

# Task 1. Investifation
Disclaimer: This challenge is inspired by a real cargo theft that occurred in March 2026, in which a shipment of KitKat products was stolen in transit between Italy and Poland. All companies, agencies, individuals, documents, and investigative findings presented in this challenge are entirely fictional. No real employees, law enforcement personnel, or organisations are implicated. The real theft remains under investigation by the relevant authorities.

Background
On 26 March 2026, a refrigerated truck carrying over 400,000 units of KitKat product vanished somewhere between Central Italy and Poland. Nestlé confirmed the theft two days later. The vehicle has not been found.

The European Cargo Threat Assessment (ECTA) does not believe this was opportunistic. A shipment of this size, on a contracted route, does not disappear without someone helping it along.

An anonymous tip reached a journalist the following evening. ECTA obtained it under judicial authority. That is where your investigation begins.

Your Assignment
You are a CZ Node investigator on Project HAVEABREAK. Your goal is to identify the culprit behind the heist by using the following files:

| File                               | Description                                      |
|------------------------------------|--------------------------------------------------|
| ecta_memo.pdf                      | Your briefing. Start here.                       |
| exhibit_a.eml                      | Exhibit A — referenced in the memo               |
| exhibit_b.jpg                      | Exhibit B — referenced in the memo               |
| transeuro_data/employees.csv       | Subpoenaed from TransEuro Logistics IT           |
| transeuro_data/access_log.csv      | Subpoenaed from TransEuro Logistics IT           |
| transeuro_data/comms_export.txt    | Subpoenaed from TransEuro Logistics IT           |


Answer the questions below
> Q1. Which VPN service was used to send the anonymous email from the .eml file?

While analyzing the `exhibit_a.eml` file, I focused on identifying the original sender IP address from the email headers.

The key line is:
```
Received: from [193.32.249.132] ([193.32.249.132])
        by smtp.gmail.com with ESMTPSA id
```
This line reveals the actual client IP that connected to Gmail. All other “Received” entries above it belong to Google’s mail infrastructure and are not useful for tracing the sender.

Next, I performed an IP lookup using an OSINT tool: `ip2proxy.com`. The IP address was associated with infrastructure commonly used by VPN providers.

Answer:
```
	Mullvad
```

<br>

> Q2. What is the full street address of the petrol station where the missing vehicle was last seen?

Initially, I tried identifying the petrol station directly using Google Maps but couldn’t find an exact match.

Revisiting the `ecta_memo.pdf`, I noticed an important clue: 
```
During canvassing of vehicles recorded on the D1 corridor on the night of 26
March 2026, a dashcam SD card was recovered from a vehicle stopped for an unrelated traffic matter near Hulín.
```

Using this, I searched for ORLEN petrol stations near Hulín and matched the location with the provided image.

Answer:
```
Kroměřížská 1281, 768 24 Hulín, Czechia
```

<br>

> Q3. At what time did the suspicious action take place in the route planning system on March 25th, 2026? <br>
Format: HH:MM:SS

Accessing the `access_log.csv` file I checked for all the files and the actions performed on them on 25th March and one action seemed suspicious: 
<img width="694" height="25" alt="image" src="https://github.com/user-attachments/assets/7bef1e43-dabb-455d-ab51-84c3a22a4cf7" />
Why would anyone want to export the route unless they are gonna use it?

Answer:
```
22:14:09
```

<br>

> Q4. What is the employee ID of the person who sent the anonymous email?

Re-reading the email provided a key hint:
```
I saw unusual activity in our internal system the night before departure.
```

Since the departure was on 25th March, I checked logs for 24th March in the file `access_log.csv`.
Only one employee accessed the system during that time window, making them the likely sender.
<img width="694" height="25" alt="image" src="https://github.com/user-attachments/assets/2ce045bc-772d-4f71-b625-c05574a13ac9" />

Answer:
```
BR-0312
```

<br>

> Q5. What is the employee ID of the employee responsible for leaking the shipment details?

From Q3, the employee who exported the route file is clearly responsible for leaking sensitive shipment information.

Answer:
```
BR-0291
```

<br>

> Q6. What is the full name of the culprit?

Using `employees.csv`, I mapped the employee ID `BR-0291` to their email address.
I then performed a reverse email lookup using `https://epieos.com`, which provided linked Google account data.
It gave me the following information:
```
{
  "metadata": {
    "query": "kraliknovak09@gmail.com",
    "timestamp": "2026-04-05T15:22:23.495Z"
  },
  "data": {
    "visitor": {
      "google": {
        "id": "103790956576446810107",
        "services": {
          "google_maps": "https://www.google.com/maps/contrib/103790956576446810107",
          "google_calendar": "https://calendar.google.com/calendar/u/0/embed?src=kraliknovak09@gmail.com",
          "google_plus_archive": "https://web.archive.org/web/*/plus.google.com/103790956576446810107*"
        }
      }
    }
  }
}
```
The first link : `https://www.google.com/maps/contrib/103790956576446810107`, itself gave the name.

Answer:
```
Radovan Blšťák
```

<br>

## 🧠 Summary
- Extracted the originating IP address from the email headers by identifying the correct “Received” entry
- Performed IP intelligence lookup to determine VPN/proxy usage
- Used contextual clues from the memo to narrow down the location and match it with map data
- Analyzed system logs to identify suspicious activity and unusual access patterns
- Correlated timestamps and behavior to link actions with specific employees
- Traced internal data access to determine who leaked sensitive shipment details
- Leveraged OSINT techniques to map an email account to a real-world identity

<br>
