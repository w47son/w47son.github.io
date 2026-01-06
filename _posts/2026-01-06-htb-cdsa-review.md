---
title: "Hack The Box CDSA Review (2026): Certified Defensive Security Analyst Exam & Course"
date: 2026-01-06 16:00:00 +0100
categories: [Review, Certification]
tags: [HTB, CDSA, Blue Team, SOC, DFIR]
image:
  path: /assets/img/commons/cdsa-review/cdsa_logo.png
  alt: Hack The Box Certified Defensive Security Analyst
---

Blue team certifications are becoming more common, but most of them are still very entry-level. That’s fine if you’re just starting out, but it quickly gets boring if you already have some experience and want a real challenge.

The **Hack The Box Certified Defensive Security Analyst (CDSA)** fills that gap. It’s designed for people who want to go beyond alerts and actually investigate attacks.

After getting the [**CCD certification**](https://w47son.github.io/posts/certified-cyberdefender-review/) and starting to work professionally, I mostly stopped caring about certs. CDSA was the only one that caught my attention. People said it had nothing to do with BTL1 and didn’t really compare to BTL2 either (which is also much more expensive). I wanted something that would actually push me, and CDSA was perfect.

![CDSA Cert](/assets/img/commons/cdsa-review/cert_cdsa.png)

## Prerequisites

Before attempting the **HTB Certified Defensive Security Analyst (CDSA)** exam, you should meet the following requirements. You can check the official details and [prerequisites here](https://www.hackthebox.com/blog/defensive-security-analyst-certification).

In short, you should master:
* Interpreting a letter of engagement.
* Having intermediate knowledge of web and infrastructure penetration testing concepts.
* Knowledge of web applications, operating systems, and networking basics.
* Comfortably navigating a plethora of data.
* Understanding the available data sources and their usage.
* Conducting manual and automated security analysis, SOC operations, and incident handling activities.
* Professionally communicating and reporting security incidents.


## What makes CDSA special?

CDSA doesn’t stay on the surface. It goes deep into **Active Directory attack detection**.

What I liked the most is that you’re forced to investigate realistic attack paths:
process injection into legitimate binaries, lateral movement, privilege escalation in AD, and finally full domain compromise as **Domain Admin**.

> **The Exam Format:** You get **7 days** of infrastructure access where you must submit a **commercial-grade professional report** detailing every phase of the intrusion.

## Course Content

The path consists of **15 modules**.
![Course Content](/assets/img/commons/cdsa-review/content.png)

While every module is important, in my experience, the most critical for passing are:
* **Detecting Windows Attacks with Splunk:** Essential for hunting AD attacks using Splunk.
* **Windows Attacks & Defense:** Crucial for identifying AD attack vectors.
* **Introduction to Digital Forensics:** Memory analysis with Volatility and disk forensics fundamentals.
* **Security Incident Reporting:** It teaches you that your investigation is useless if you cannot explain it to both technical and non-technical clients.

## Price

At the time of writing, the **Silver Annual plan** costs around **€410/year**.
*(prices may vary depending on active promotions)*

It includes:
* **All courses** in this path (and others)
* The **CDSA exam voucher** (valid for one year)
* **Two exam attempts**

## Preparation

My approach was simple and effective:
* Complete the path **100%**
* Understand every technique (not just follow steps)
* Take detailed notes in **Notion** (or your preferred tool) and **save every SIEM query**.


Tools you must master include:
* [**Eric Zimmerman Tools:**](https://ericzimmerman.github.io/#!index.md) Specifically **Timeline Explorer**, which was my best friend for bringing order to the chaos of artifacts.
* **SIEMs (Splunk and Elastic):** You need total fluency in correlating events within the labs.

In my case, I didn't need extra labs like *Sherlocks*. The path content was sufficient.
> **Bonus:** I discovered a cool tool called [**Sysmon View**](https://github.com/nshalabi/SysmonTools), which allows you to visualize Sysmon logs graphically.

## Requirements to Pass

To earn the certificate, you must:
* **Score at least 80 points:** You must find and report at least **17 out of 20 flags** in Incident 1.
* **Submit a Commercial-Grade Report:** A CTF writeup is not acceptable. You must include an **Executive Summary**, **Root Cause Analysis**, **IoCs**, and an extremely detailed **Technical Timeline** for both incidents.

You can see the expected structure here:  
<https://docs.sysreptor.com/assets/reports/HTB-CDSA-Report.pdf>

## The Exam

I started the exam on a Saturday, and by Sunday, I had resolved both technical incidents. I had some personal issues unrelated to the exam, but managed to push through and focus on the report afterward.

From Monday to Friday, the hardest part, without a doubt, was the **reporting**. Doing it while working full-time and living alone is a brutal time management challenge. I made the mistake of forgetting to take some screenshots during the live analysis. That made organizing evidence later much harder.

> **Golden Advice:** Capture *everything* in the moment. It is better to spend time organizing later than to realize you are missing a crucial piece of evidence.

## What I Learned (and You Will Too)

CDSA significantly improved my AD attack analysis skills. For example:

* Detecting **Kerberoasting** by identifying weak encryption types such as **RC4**
* Applying math to defense, like **outlier detection using standard deviation in Splunk** to find anomalous executions or traffic

This article explains the concept well:  
<https://discoveredintelligence.com/quick-guide-to-outlier-detection-in-splunk/>



## Conclusion

If you want to work in a SOC at a serious level and stop being just an “alert analyst”, **CDSA is absolutely worth it**.

It teaches you how to investigate, think critically, and report like a professional.

You’ll need time and a solid analyst background. The exam is intense, realistic, and demanding but that’s precisely why it’s good.