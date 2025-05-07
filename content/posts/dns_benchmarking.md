---
title: "DNS Benchmarking"
date: 2025-05-07
draft: false
tags:
  - DNS
  - Benchmarking
  - CLI
  - Go
  - DNSSEC
---

I'm happy to announce a simple yet effective command-line DNS benchmarking tool I've recently developed: **dns-benchmark**. This project was directly inspired by the outstanding work of [Steve Gibson](https://www.grc.com/dns/benchmark.htm) and his well-known DNS Benchmark utility. Steve’s meticulous attention to detail in benchmarking DNS performance motivated me to create something similar, but tailored specifically for the command line.

This CLI tool, written in Go, allows individual users to quickly test and compare various recursive DNS servers, checking key metrics such as latency, reliability, DNSSEC validation, and even response correctness—like detecting servers that hijack NXDOMAIN responses.

### What is a Recursive DNS Server?

A recursive DNS server acts as an intermediary between your device and authoritative DNS servers on the internet. When you request a domain name (like www.example.com), a recursive DNS server queries multiple authoritative servers on your behalf to find the corresponding IP address.

Typically, your Internet Service Provider (ISP) provides the recursive DNS server you use by default. However, ISPs generally have limited incentives to optimize these DNS servers for high performance or security. For ISPs, DNS servers often represent a cost center rather than a strategic asset. Additionally, ISP-operated DNS servers are frequently subject to regulatory requirements (such as blocking certain domains that aren't allowed in your country) or financial considerations, like redirecting queries for non-existent domains (NXDOMAIN hijacking) to monetized search or advertising pages.

While changing your recursive DNS server is typically straightforward, assessing whether a new DNS server is actually the right choice for you can be challenging, especially considering your geographic location within the network. For example, if you're located in Europe, choosing a DNS server based in the US might result in higher latency due to increased packet round-trip times. Tools like **dns-benchmark** help simplify this evaluation by providing clear performance metrics tailored specifically to your network environment.

A typical flow of DNS queries to get an IP address involves:

1. Your device asks your configured recursive DNS server, "What's the IP for www.example.com ?"
2. The recursive DNS server checks its cache. If not cached, it asks a root DNS server, "Who knows about .com?"
3. The root server replies with the address of a .com authoritative server.
4. The recursive DNS server queries the .com server, "Who knows about example.com?"
5. The .com server replies with the address of the authoritative DNS server for example.com.
6. Finally, the recursive DNS server queries the authoritative server for example.com, "What's the IP for www.example.com ?"
7. The authoritative DNS server replies with the IP address, which the recursive DNS server returns to your device.

Your device can then connect directly to the provided IP address to access the desired content.

### Technical Details and Development Trade-offs

In developing **dns-benchmark**, several design and ethical considerations were carefully balanced:

* **Concurrency and Rate Limiting:** While Go offers powerful concurrency features, I intentionally limited parallel queries and included rate limiting to avoid inadvertently overwhelming DNS servers.
* **Cached vs. Uncached Queries:** To provide meaningful results without excessive traffic, the tool performs a balanced mix of cached (popular domains) and uncached (randomized) queries. This approach yields realistic latency measurements without unnecessarily taxing DNS infrastructure.
* **Reliability and Correctness Checks:** Additional checks were included for reliability (detecting dropped responses) and correctness (identifying NXDOMAIN hijacking and DNSSEC validation), enhancing the depth of insights provided.

I'm not a software engineer by profession, but driven by curiosity and the goal of enhancing my own experience, I've put significant care into making this tool both user-friendly and respectful of public DNS infrastructure. Following Steve Gibson’s principles of ethical testing, dns-benchmark ensures it doesn't unintentionally stress public resolvers.

Whether you're a power user looking for speed, someone interested in DNS security features, or simply curious about DNS performance, **dns-benchmark** provides a clear, concise, and effective way to evaluate your options.

Feel free to check it out, contribute, or give feedback:

[https://github.com/taihen/dns-benchmark](https://github.com/taihen/dns-benchmark)

Happy benchmarking!