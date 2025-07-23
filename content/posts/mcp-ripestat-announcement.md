---
title: "MCP RIPEstat: Ask Your Network Questions in Plain English"
date: 2025-07-23T17:15:00+02:00
lastmod: 2025-07-23T17:15:00+02:00
draft: false
tags:
  - networking
  - automation
  - go
  - infrastructure
  - monitoring
  - security
  - bgp
  - ripe
  - sysadmin
---

Ever found yourself knee-deep in a routing incident at 3 AM, frantically switching between terminals and browser tabs trying to piece together BGP data? **I've been there**. You're chasing down a routing anomaly, your coffee's gone cold, and you're clicking through endless forms just to figure out which AS is announcing that suspicious prefix.

What if I told you that instead of navigating web forms and memorizing API endpoints, you could just ask: _"Who's announcing 1.1.1.1 and when did they start?"_ or _"Show me the routing history for this IP that's been acting weird"_ - and get instant, detailed answers?

That's exactly what [MCP RIPEstat](https://github.com/taihen/mcp-ripestat) does. It's a bridge between the incredibly powerful [RIPEstat Data API](https://stat.ripe.net/docs/data_api) and natural language, built specifically for network engineers who have better things to do than remember API documentation.

## Why Should Network Engineers Care?

If you've spent any time troubleshooting Internet routing, you know the drill:

**The Old Way:**

- Open
  - Terminal and login to a BGP router, start looking at your AS routing snapshot
  - RIPEstat and some looking glass services from most known providers
- Navigate through menus to find the right tool
- Fill out forms with IP addresses or ASNs
- Copy/paste results between browser windows and terminal
- Repeat for every piece of related data you need
- Try to correlate everything manually

**The New Way:**

```
User: "What's the abuse contact for 8.8.8.8?"
Response: network-abuse@google.com

User: "Show me AS 5617's announced prefixes"
Response: Orange Polska (AS5617) announces 81 prefixes including:
- 31.60.0.0/14 (your current network!)
- 80.48.0.0/13
- 213.77.0.0/16
- And 78 more...
```

No clicking, no forms, no API docs. Just ask what you need to know.

## Real-World Network Intelligence

Let me show you what this looks like with actual network data. These are real queries I ran while writing this post:

### Quick Network Reconnaissance

```
> "What can you tell me about my current IP?"
Your IP 31.61.175.27 belongs to:
- Network: 31.60.0.0/14
- AS: 5617 (Orange Polska)
- Organization: TPNET - Orange Polska Spolka Akcyjna
```

**Why this matters:** Instant network context. No more digging through whois databases or trying to remember which tool gives you the cleanest AS information.

### Routing Anomaly Investigation

```
> "Show me the routing history for 1.1.1.1 over the past 3 weeks"
Cloudflare (AS13335) has been consistently announcing 1.1.1.0/24:
- Stable visibility: 340-346 full peers throughout July
- No routing instability detected
- Consistent announcement patterns
```

**Why this matters:** When you're investigating a routing issue, you need to know if something changed. This gives you the timeline instantly, without having to parse raw BGP data.

### Security Incident Response

```
> "I need the abuse contact for 8.8.8.8"
network-abuse@google.com
```

**Why this matters:** When you're dealing with abuse or need to contact another network's NOC, finding the right contact shouldn't take 10 minutes of clicking through databases.

## How It Actually Works

MCP RIPEstat implements the [Model Context Protocol](https://modelcontextprotocol.io/introduction) - think of it as a standardized way for AI assistants to talk to specialized tools. Instead of building yet another web interface, it plugs directly into tools like [Claude Code](https://claude.ai/code), [Cursor IDE](https://cursor.sh/), or any MCP-compatible client.

**The Architecture That Actually Makes Sense:**

- **Built in Go:** Single binary, no dependencies, deploys anywhere
- **HTTP Transport:** Real streaming protocol, not polling
- **Rate Limited:** Respects RIPE's API limits automatically
- **21 of 64 endpoints:** Covers most of the tools you use daily

The beauty is that it's not trying to replace RIPEstat's web interface - it's making the _data_ accessible in a completely different way.

## Beyond Basic Lookups

Sure, you can ask simple questions, but the real power comes from complex queries that would normally require multiple API calls and manual correlation:

### Complex AS Analysis

```
> "Tell me about AS 5617's network footprint"
Orange Polska (AS5617) operates a substantial network:
- 81 announced prefixes covering both IPv4 and IPv6
- Major blocks include consumer broadband ranges
- Active presence in Polish Internet infrastructure
- Mixed /13 to /24 announcements suggesting hierarchical allocation
```

### BGP Path Analysis

```
> "Has Cloudflare's 1.1.1.1 routing been stable this month?"
Extremely stable - consistently seen by 340+ full BGP peers
with only minor visibility fluctuations (±6 peers) typical
of normal Internet routing dynamics.
```

**This is the kind of analysis that normally takes 20 minutes of clicking and cross-referencing.** Now it's a single question. For more examples of what you can ask, check out the [example prompts](https://github.com/taihen/mcp-ripestat/blob/main/PROMPTS.md) in the repository.

## Real-World Outage Analysis: The Facebook BGP Withdrawal

Let me walk you through how MCP RIPEstat transforms complex incident response from a 2-hour investigation into a 10-minute analysis. We'll use the Facebook global outage from October 4, 2021 - the day Facebook accidentally withdrew all their BGP routes and took down not just Facebook, but Instagram, WhatsApp, and even their internal systems.

### The Incident: What Actually Happened

On October 4, 2021, Facebook's engineering team was performing routine maintenance on their backbone routers. A command intended to assess available capacity accidentally took down all the connections in their backbone network, withdrawing all Facebook routes from the global Internet.

**The Traditional Investigation Approach:**

When Facebook went dark, network engineers worldwide would have:

1. Checked their own routing tables manually
2. Visited multiple BGP looking glasses
3. Cross-referenced data from different RIRs
4. Manually correlated routing announcements
5. Spent lots of time piecing together the timeline

**The MCP RIPEstat Approach:**

Let's reconstruct what this analysis would look like with natural language queries:

### Step 1: Initial Assessment

```
> "What can you tell me about Facebook's main AS?"

Facebook (AS32934) is a major Internet presence with extensive global infrastructure.
Network holder: FACEBOOK
Status: Currently announced with normal visibility
Block assignment: ARIN-managed (32768-33791 range)
```

### Step 2: Route Visibility Investigation

```
> "Show me the routing history for Facebook's main infrastructure around October 4, 2021"

Analyzing 31.13.64.0/18 (Facebook's core infrastructure block):
- Oct 1-4: Stable visibility ~340-342 full BGP peers
- Oct 4 16:00 UTC onwards: Maintained visibility
- Oct 5-6: Returned to normal ~340 peers
- Minor fluctuations typical of normal routing dynamics
```

**Why This Matters:** This instantly shows you the incident timeline. The routing data reveals that while there were some fluctuations around October 4-5, 2021, the specific /24 we queried maintained relatively stable visibility - but this is looking at aggregate data. During the actual outage, more specific prefixes would have shown complete withdrawal.

### Step 3: Multi-Prefix Correlation Analysis

```
> "Compare routing stability for Facebook's edge networks vs core infrastructure"

Core Infrastructure (31.13.64.0/18):
- Maintained consistent BGP visibility throughout analysis period
- 340+ peer visibility indicates tier-1 connectivity

Edge Networks Analysis Needed:
- Would require checking multiple /24s within the larger block
- More specific prefixes likely showed the actual withdrawal pattern
```

### Step 4: AS Relationship Analysis

```
> "Who are Facebook's main BGP neighbors and upstream providers?"

Facebook (AS32934) BGP relationships:
- Extensive peering relationships globally
- Multiple tier-1 upstream connections
- Direct peering at major IXPs worldwide
- Complex mesh of provider and peer relationships
```

### The Real Investigation Power

**What took hours in 2021 now takes minutes:**

Instead of manually checking dozens of looking glasses and trying to correlate data across different time zones, you can ask:

```
> "Show me a complete routing timeline for all Facebook prefixes from October 3-5, 2021, and identify any announcements that disappeared completely during business hours"

Analyzing Facebook's 157.240.0.0/16 infrastructure block during the October 4, 2021 outage:

🚨 CRITICAL ROUTING ANOMALIES DETECTED:

157.240.207.0/24 (AS32934) - MAJOR WITHDRAWAL EVENT:
• Oct 3-4 15:59 UTC: Normal visibility (341 full peers)
• Oct 4 16:00-23:59 UTC: Catastrophic drop to 23 peers (-93% visibility)
• Oct 5 00:00+ UTC: Full recovery (341 peers restored)

157.240.159.0/24 (AS63293) - SUSTAINED DEGRADATION:
• Oct 3-4 07:59 UTC: Normal visibility (342 full peers)
• Oct 4 08:00 - Oct 5 07:59 UTC: Reduced to 219 peers (-36% visibility)
• Oct 5 08:00+ UTC: Recovery to 341 peers

TIMELINE ANALYSIS:
The data shows clear evidence of Facebook's BGP withdrawal starting Oct 4 around 16:00 UTC (11am PST) - exactly when their maintenance began. The 157.240.207.0/24 prefix experienced near-complete global invisibility, dropping from 341 to just 23 BGP peers, indicating widespread route withdrawal across major Internet exchanges.

This confirms the incident timeline reported in Facebook's post-mortem.
```

**Cross-validation becomes trivial:**

```
> "Compare Facebook's routing announcements with their main competitors during the same timeframe - did anyone else experience similar issues?"

Comparative BGP Analysis Oct 3-5, 2021:

Facebook Infrastructure (AS32934):
- Significant routing disruptions on Oct 4 16:00-23:59 UTC
- Multiple prefixes showed dramatic visibility drops
- Pattern indicates coordinated withdrawal, not random failures

Other Major Content Networks:
- Google (AS15169): Maintained stable routing throughout period
- Cloudflare (AS13335): No visibility anomalies detected
- Amazon (AS16509): Normal routing patterns observed

CONCLUSION: The Oct 4 routing issues were isolated to Facebook's network infrastructure - this was an internal configuration problem, not a broader Internet stability issue.
```

**Impact assessment becomes instant:**

```
> "Which networks typically use Facebook as a transit provider, and how would they have been affected by a complete BGP withdrawal?"

Facebook Transit Impact Assessment:

Direct Impact:
- Facebook operates as origin AS (32934) primarily, not transit provider
- Limited downstream customer impact compared to tier-1 carriers
- Primary impact: Loss of access TO Facebook services, not THROUGH Facebook

Affected Services:
- Facebook.com, Instagram, WhatsApp applications
- Facebook's CDN edge networks (multiple ASNs: 63293, 35819, 33667, etc.)
- Third-party sites using Facebook Connect/OAuth

Network Engineering Impact:
- NOCs worldwide spent resources investigating "their" routing issues
- Increased support tickets from users reporting connectivity problems
- Unnecessary troubleshooting of local infrastructure

The real impact was service availability, not transit routing - Facebook's customers couldn't reach Facebook, rather than Facebook disrupting other networks' connectivity.
```

### The Technical Reality Check

The Facebook outage was particularly devastating because:

1. **Complete BGP Withdrawal**: All routes disappeared simultaneously
2. **Internal Impact**: Their own engineers couldn't access internal systems
3. **DNS Resolution Failure**: Even facebook.com stopped resolving
4. **Physical Access Required**: Teams had to physically access data centers

With MCP RIPEstat, you could have immediately confirmed:

- **Route Withdrawal Timeline**: Exact timestamps of when prefixes disappeared
- **Global Impact Scope**: Which networks lost connectivity to Facebook properties
- **Recovery Pattern**: How routes were gradually re-announced
- **Comparative Analysis**: Whether similar patterns affected other major networks

### Beyond the Headlines

This kind of analysis reveals insights that don't make the news:

```
> "Show me the recovery pattern - did Facebook bring back all their routes simultaneously or in phases?"

Recovery Analysis:
- Core infrastructure: Gradual re-announcement starting ~17:50 UTC
- Edge networks: Phased rollout over 30-minute window
- Geographic distribution: US East Coast first, then West Coast and international
- Full stability: Achieved by 18:30 UTC with normal peer visibility
```

**This is intelligence you can't get from reading incident reports** - it's the actual network behavior that only routing data reveals.

## Getting Your Hands Dirty

### Local Installation

If you're the "build it yourself" type (and honestly, in networking, we usually are):

```bash
git clone https://github.com/taihen/mcp-ripestat
cd mcp-ripestat
make build
./mcp-ripestat --port 8080
```

### Try It Without Installing

There's a demo server running at `https://mcp-ripestat.taihen.org/mcp` - no uptime guarantees, but perfect for testing.

### Integration with Your Workflow

**Claude Code users:** Add this to your MCP transport commands:

```bash
claude --mcp-transport https://mcp-ripestat.taihen.org/mcp
```

**Cursor IDE users:** Update your `~/.cursor/mcp.json`:

```json
{
  "ripestat": {
    "command": "mcp-ripestat",
    "args": ["--port", "8081"]
  }
}
```

## The "But Why?" Question

Look, we already have excellent tools. RIPEstat's web interface is comprehensive. The API is well-documented. [BGP.he.net](https://bgp.he.net/) exists. So why build another layer?

**Because context switching is expensive.**

When you're in the middle of troubleshooting, every tool switch, every new browser tab, every "wait, which form field was the AS number again?" adds cognitive load.

MCP RIPEstat isn't about replacing your existing tools - it's about reducing the friction when you need network intelligence _right now_, in the context of whatever else you're working on.

**Because natural language scales better than forms.**

Try asking a web form: _"Show me any routing instability for networks in Netherlands over the past week."_ You can't. But with natural language interfaces, complex queries become possible.

**Because the AI revolution is happening whether we like it or not.**

Network engineering is becoming more automated, more intelligence-driven. Tools that bridge our specialized knowledge with AI capabilities aren't just convenient - they're strategic advantages.

## What's Actually Implemented

The current version covers the network intelligence functions you use most:

**Core Functionality:**

- AS overviews and prefix announcements
- Network information and routing status
- BGP routing history and path analysis
- WHOIS data and abuse contact lookup

**Advanced Analysis:**

- Routing consistency monitoring
- Historical allocation tracking
- Geographic ASN mapping
- RPKI validation status

**What's Missing (For Now):**

- RIS routing table dumps
- Geolocation data
- DNS reverse lookups
- RIPE Atlas test scheduling (not a RIPEStat feature)

The full roadmap does not cover all 64 RIPEstat endpoints, some are very useful - some less, I've prioritized by what network engineers actually use in practice. See the complete [API coverage status](https://github.com/taihen/mcp-ripestat#api-coverage) for details.

## Security Reality Check

Let's be honest about the current state: **there's no authentication**. The original MCP spec didn't include it, and while newer versions have OAuth 2.1 support, adoption is still limited.

For production use, just run it locally.

Bu if you want to share it with your team, you'll want network-level controls:

- Firewall, VPN, and other network controls
- MCP proxy with proper auth

See the [security considerations](https://github.com/taihen/mcp-ripestat/blob/main/README.md#security) in the documentation for detailed deployment guidance.

## Final Thoughts

Network engineering is already complex enough. We spend our days thinking about AS paths, BGP policies, and routing convergence - not about which web form gets us the data we need.

MCP RIPEstat is about removing friction from network intelligence gathering. It's about asking questions in plain English and getting answers that help you understand what's happening in your corner of the Internet.

**The Internet's routing table changes 150,000+ times per day.** Having tools that help you understand those changes quickly isn't just convenient - it's essential.

Try it out, break it, [tell me](https://taihen.org/contact/) what's missing or create GitHub issue. The networking community built the Internet by sharing knowledge and tools. This is just the latest evolution of that tradition.

**Want to contribute?** Check out the [contribution guidelines](https://github.com/taihen/mcp-ripestat/blob/main/CONTRIBUTING.md) or browse the [open issues](https://github.com/taihen/mcp-ripestat/issues) to see what needs work.

**Now go fix some routing! The Internet needs you!**
