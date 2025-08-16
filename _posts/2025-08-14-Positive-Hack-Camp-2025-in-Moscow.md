# Positive Hack Camp 2025 — Moscow

## How I Ended Up in a Hacker Camp in Moscow

### The question I get the most: *“Wan Yin, how did you even hear about Positive Hack Camp 2025?”*

The short answer? — **Through Connection.**

The long answer? 

I joined the Malaysia Cybersecurity Camp (MCC) back in 2024 and got introduced to **RE:HACK** — a cybersecurity community. One day, Mr. Ahmad Ashraff bin Ahmad shared a link about this “Positive Hack Camp” in Moscow through the community Discord and that tiny moment snowballed into a full-on adventure.

### My reaction when I got accepted  

Honestly? **Shocked** and **grateful**. Initially, I didn’t even plan to register — I mean, Moscow? That’s halfway across the planet and I don't even speak the language. But one of my seniors persuaded me, and I thought, *“Well, there’s no harm in trying.”* 

Fast forward a few weeks, and I’m sitting here in Russia. Talk about the butterfly effect 🦋 — from a Discord ping to standing in front of the Red Square, wondering how I got here.


## Experience of the Camp 🚩

### Let me just say this: *VIP Treatment for Broke Students*

Here’s the thing: if this camp wasn’t free, I wouldn’t be here. Period. 🙃

Positive Hack Camp covered everything except the flight — and when I say everything, I mean:

- **Free 5-star hotel stay** — I thought we’d have to share rooms, but nope. Each of us got our own individual room. I nearly cried.

- **Free mealsss** — We’re talking fancy dinners, beautiful buffets, and enough variety to make me forget my own kitchen back home. (Jk, I still misses Malaysian food very much 😋)

- **Free transport** — Shuttle buses, private coaches, all covered.

- **Free training** — Actual hands-on hacking lessons taught by professionals.

- **Free tours** — They didn’t just lock us in a classroom; we actually got to see Moscow!!! 

Positive Technologies must have a magic amount of fund because they treated us like VIP guests. I mean, one minute I’m a broke diploma student in Kuala Lumpur, the next I’m sipping soup in a chandelier-lit dining hall in Moscow. Life is wild.

And yes — on arrival, I was still suspicious. Seventeen hours of flying and jet lag had me side-eyeing everything. For a good few hours, I wondered if I had just fallen for the world’s most elaborate cybersecurity scam. I didn’t fully relax until we checked in and they handed me my own keycard for my own hotel room.

## Let's Get Technical 💻

Each day followed a simple but effective rhythm: **two speakers, one topic, and a lot of hacking (the legal kind).**
First, we’d dive into the theory — slides, demos, and expert explanations to set the stage. Then came the fun part: **hands-on challenges.**

These challenges worked like **mini CTFs**: find the hidden flag, prove you understood the lesson, and maybe earn some bragging rights on the scoreboard. It kept things lively, tested our skills on the spot, and made sure no one could just “nod along” without actually learning.

---

### Day 1 — Exploration in the Customer's External Infrastructure and Social Engineering

Our first day at Positive Hack Camp kicked off with a lesson in **reconnaissance** — basically, the hacker’s version of people-watching, except the “people” are networks, domains, and servers. The goal? Gather as much intel as possible before making any moves.

**Active vs Passive Reconnaissance**

- **Active reconnaissance** → The loud approach. Port scanning, domain name enumeration…, interacting directly with the target. Great for penetration testing, but leaves digital footprints.

- **Passive reconnaissance** → The silent ninja. Internet monitoring, database analysis, and open-source intelligence gathering. No direct contact with the target, so you’re much harder to detect.

When doing recon, hackers usually hunt for:
1. Domain names
2. Active hosts
3. Open ports
4. Operating systems in use
5. Software versions
6. Technology stack

**Penetration Testing vs Red Teaming**

One key takeaway:

- **Penetration testing** → Short, intense, and with more upfront knowledge. Think of it like stress-testing a system.

- **Red teaming** → Slow and stealthy. Simulates a real-world attack to test both security detection and incident response. It can take weeks or even months.

**Our Recon Toolbox**

- Domain intel services → `whois`, `SPARK`, `RIPE`

- Subdomain hunting:

  - DNS Zone Transfer → If a server is misconfigured, you can download the DNS zone file.
      - Tools: `dig`

  - DNS Brute Forcing → Guessing subdomains using dictionaries.

    - Tools: `nslookup`, `subfinder`, `amass`, `sublist3r`, `assetfinder`

- Passive search resources → using public online resources

  - dnsdumpster.com
  - shodan.io
  - censys.io
  - crt.sh
  - pentest-tools.com

We wrapped up the day practising with nmap and dig for zone transfers on the challenges provided. By the end, I realised recon isn’t just technical — it’s part science, part creativity.

---

### Day 2 — Hacking Web Applications

The second day was all about web application security. We explored how modern applications handle requests and responses, and then moved into practical exploitation techniques.

**Understanding HTTP Requests and Responses**

Before diving into attacks, we needed a solid foundation of how web communication works:

- HTTP Request contains:

  - Method → GET, POST, PUT, DELETE

  - Path → e.g. /login.php

  - Protocol → HTTP/1.1, HTTP/2, etc.

  - Headers → Content-Type, Cookie, etc.

  - Body (optional) → Usually present in POST requests

- HTTP Response contains:

  - Protocol → e.g. HTTP/1.1

  - Status → 200 (OK), 404 (Not Found), 302 (Redirect), etc.

  - Status Message → Short text for the code

  - Headers → e.g. Set-Cookie, Content-Type

  - Body → The actual response data (HTML, JSON, etc.)

👉 Fun fact: when editing HTTP requests manually in Burp Suite, don't delete the two empty lines at the end — otherwise, the request won’t be processed correctly. Easy to overlook, but important.

**Exploitation Techniques Covered**

We then moved on to classic web vulnerabilities:

- **OS Command Injection (Shell Injection)**: Executing system commands through vulnerable input fields.

- **SQL Injection (SQLi)**: Injecting malicious SQL queries to read or modify database data.

  - Practised using `UNION SELECT` and `GROUP_CONCAT` to enumerate columns, tables, and extract data.

- **Path Traversal**: Accessing files outside the intended directories by manipulating file paths (e.g., ../../etc/passwd).

- **XML External Entity Injection (XXE)**: Injecting or editing the DOCTYPE element in XML to load external entities, then retrieving sensitive data.

- **Insecure Direct Object References (IDOR)**: Exploiting predictable identifiers in URLs or requests to access data belonging to other users.

*Pro tip: inserting plain text commands doesn’t always work. Sometimes, sanitisation blocks you, so I have to use “creative replacements” like ${IFS} for a space.*

I noticed that Cross-Site Scripting (XSS) was listed on the learning platform, but due to time constraints, it wasn’t covered that day. I guess that means it’s self-study time!

**Tools and Techniques**

- Burp Suite → Our main tool for intercepting and modifying requests.

- Brute force tools → `patator`, `medusa`, `hydra`, `OWASP ZAP`.

- Directory discovery → `ffuf`, `dirsearch` (requires a good wordlist).

Day 2 really showed how fragile web applications can be if proper input validation and sanitization aren’t enforced. Even small mistakes in request handling can lead to serious compromises.

---

### Day 3 — Exploit Known Vulnerabilities in Network Services
