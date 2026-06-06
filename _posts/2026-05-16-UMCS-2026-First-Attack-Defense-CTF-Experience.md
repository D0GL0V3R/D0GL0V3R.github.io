# My First Attack-Defense CTF Experience: Getting Absolutely Destroyed at UMCS 2026

![AD Platform](/static/images/AD%20Platform.jpeg)

A few weeks ago, my team, **Bob**, participated in the finals of UMCS CTF 2026. Looking back, it was probably one of the most brutal cybersecurity competitions I've ever experienced.

And honestly?

We got absolutely destroyed.

But it was also one of the most valuable learning experiences I've had so far.

The competition consists of two stages. The first stage was a traditional online Jeopardy-style CTF where teams solved challenges to earn points. The top 15 teams would qualify for the finals, which would be held physically at the University of Malaya in an Attack-Defense format.

We made it through the first round and the excitement lasted right up until we realised that none of us had ever played an Attack-Defense CTF before.

To make things even more interesting, the final round strictly prohibited the use of AI tools. For experienced players, this probably wasn't a big deal. For us, however, it removed a resource that could have helped us understand unfamiliar concepts and troubleshoot problems quickly during the competition. Since we were complete beginners in Attack-Defense, every mistake would have to be figured out the traditional way.

Still, we weren't going to give up before even starting.

---
## Our Strategy

A few days before the finals, our team started researching how Attack-Defense competitions worked. After reading various write-ups and watching videos, we came up with what we thought was a solid strategy.

- Two of us would focus on defence.

- Two of us would focus on offence.

For defence, our goal was to monitor Docker logs, inspect network traffic, and, if we got lucky, capture exploit scripts used by other teams.

For offence, we planned to implement a simple Web Application Firewall (WAF) that would use regex rules to detect malicious requests and drop the request from teams attempting to steal our flags.

At the time, it sounded brilliant.

In hindsight, we had absolutely no idea what we were doing.

---
## So it Begins ...

The competition started.

Within minutes, our services were already being attacked.

I was responsible for network monitoring, so I immediately fired up Wireshark and started listening on the VPN interface. 

![Wireshark Monitoring](/static/images/Wireshark%20Monitoring.jpeg)

Nothing useful appeared.

I switched to tshark.

Still nothing.

I even created a monitoring dashboard that tracked incoming and outgoing IP addresses, suspicious commands, and network activity.

![Monitoring Dashboard](/static/images/Monitoring%20Dashboard.jpeg)

Nothing.

Meanwhile, other teams seemed to know exactly what they were doing. Our services kept getting hit, and we couldn't even see how they were being attacked.

---
## What Went Wrong ...

After the competition, I spoke to one of the challenge creators and finally understood why.

The vulnerable services were running inside Docker containers. I had been monitoring the host machine. The traffic I was interested in was flowing through Docker's virtual networks rather than the interfaces I was watching. In other words, I was looking in completely the wrong place. It was one of those moments where everything suddenly made sense.

The same thing happened with Docker logs.

Before the competition, we assumed logs would reveal attack attempts and suspicious behaviour. Instead, they turned out to be mostly useless. Most attacks weren't generating obvious errors. Many exploits interacted directly with the application logic. Some attacks succeeded without leaving any meaningful trace in the logs. We spent a significant amount of time staring at logs that told us almost nothing.

While the defensive side was struggling to identify attacks, we made another major mistake -- We started patching vulnerabilities before understanding what the vulnerabilities actually were.

Instead of analysing how teams were attacking us, we immediately began implementing our WAF idea. The problem was that we didn't know what payloads we were trying to block. We didn't know what the exploit looked like. We didn't even know which vulnerability was being abused. We were essentially trying to solve a mystery by randomly placing bandages everywhere and hoping one of them worked.

To make matters even worse, our WAF ended up consuming a ridiculous amount of system resources. By the end of the competition, we had somehow managed to use all 8GB of RAM available on the target machine. Even the challenge creator was surprised.

As the competition progressed, reality started to sink in.

- We weren't successfully defending.

- We weren't successfully attacking.

- We weren't stealing flags.

- We weren't patching vulnerabilities effectively.

Most of our effort went into making sure our Docker containers stayed alive so we wouldn't lose points from service downtime. At some point, restarting containers became our primary defensive strategy. It wasn't exactly the elite cyber warfare experience we had imagined. When the competition ended, we placed 11th out of 15 teams. Objectively speaking, that's not a great result.

---
## After Thoughts

Attack-Defense CTFs expose weaknesses that Jeopardy-style competitions often hide. In a Jeopardy challenge, you focus on solving one problem at a time. In Attack-Defense, you're expected to monitor systems, understand vulnerabilities, patch services, analyse attacks, maintain uptime, develop exploits, and steal flags simultaneously.

It's chaotic.

It's stressful.

And it's incredibly fun.

More importantly, every mistake taught us something. I learned that visibility is everything. You can't defend what you can't see. I learned that patching without understanding the vulnerability is usually a waste of time. I learned that Docker networking matters far more than I previously thought. Most importantly, I learned that Attack-Defense CTFs require a completely different mindset from traditional CTFs.

Would we perform better if we competed again?

Absolutely.

Would we still make mistakes?

Probably.

But at least next time we'll know that blindly staring at the host network interface while Docker containers are being attacked isn't going to get us very far.

UMCS 2026 was our first Attack-Defense CTF. We got cooked.

But we learned more in those few hours than we could have learned from weeks of reading write-ups. And honestly, I can't wait to do it again.

Shoutout to my teammates - Eric Hendryani, Koh Tom Han and Teh Yu Xuan.
