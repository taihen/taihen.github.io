---
title: "What ever happend to IPv5"
date: 2013-01-25T22:36:36+01:00
draft: false
---

Back in 1994, a new Internet Protocol was in the works - **Internet Protocol next generation (IPng)** - which eventually became **IPv6**. The goal was to have it up and running by 1996, with the idea that by the time IPv4 addresses ran out, everyone would already be using IPv6. Well… that didn’t quite go as planned. As of last year (2012), global adoption of IPv6 only just hit **1%**.

Interestingly, in the early days, IPng was actually labeled as **version 7**. But skipping version numbers-especially to an odd one - felt irrational, so it was quickly corrected to **version 6** around the 26th IETF meeting. IPv6 was meant to be the savior of the internet, replacing the well-worn and rapidly depleting IPv4. It did this mainly by expanding address space from **32 bits to 128 bits**, which seemed like an overwhelming amount at the time. IPv6 also introduced **anycast routing**, removed checksums from the IP layer, and made several other improvements.

Yet, despite these changes, IPv6 still includes an **8-bit version field** that identifies the protocol version. IPv4 packets have a "4" in that field, so logically, IPv6 should have been labeled with a "5." But there was a problem - **version 5 was already taken**.

### So… What Happened to IPv5?  

To answer that, we have to go back to the **late 1970s**. A protocol called **The Internet Stream Protocol (ST)** was developed as an alternative to IP, specifically to support streaming services like voice and video. Unlike IPv4, which is **connectionless**, ST was built to **maintain connections** and ensure Quality of Service (QoS). It first appeared in **[IEN 119 (1979)](https://www.rfc-editor.org/ien/ien119.txt)**, and nearly two decades later, it was revised as **[RFC 1190 (ST2)](https://datatracker.ietf.org/doc/html/rfc1190)** and later **[RFC 1819 (ST2+)](https://datatracker.ietf.org/doc/html/rfc1819)**. Major companies like **Apple and IBM** even experimented with it.  

The key difference? **ST2 and ST2+ maintained state in every router**, much like **Resource Reservation Protocol (RSVP)**. This meant routers had to remember all active connections, making QoS guarantees possible. But in reality, this was a **nightmare** - keeping track of connections required **too much memory and processing power**. Worse, if a single router failed, all connections had to be **reestablished from scratch**. Unsurprisingly, ST **never took off**.

However, in **[RFC 1700 (1994)](https://datatracker.ietf.org/doc/html/rfc1700)**, ST2 was assigned **version 5**—which meant IPv5 was officially **taken**. So, when the time came for the next major IP version, they had no choice but to **jump straight to IPv6**.  

### The Curious Case of 64-Bit Addresses  

There’s one more interesting possibility I hadn’t really considered before. IPv4 uses **32-bit addresses**, and IPv6 uses **128-bit addresses**, but what about **64-bit addresses**? It’s possible that, at some point, someone mapped out a logical progression:  

- **IPv4 → 32-bit addresses**  
- **IPv5 → 64-bit addresses**  
- **IPv6 → 128-bit addresses**  

But that doesn’t seem to be the case. Historically, tech folks tend to reserve **odd numbers** for experimental protocols, which might explain why IPv5 was given to ST2 instead of a mainstream IP version.  

Either way, **IPv6 is here to stay** - even if it's taken decades longer than expected to catch on.
