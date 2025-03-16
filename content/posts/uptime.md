---
title: "May your uptime be high!"
date: 2014-06-02T22:36:36+01:00
draft: true
tags: ["uptime", "sysadmin", "server", "networking", "security"]
lastmod: 2023-10-10T00:00:00+01:00
---

### The 2,487-Day Server

In the early 2000s, sysadmins took pride in server uptimes. I once had a colleague who proudly told me about a RHEL 5 server that ran non-stop for **2,487 days**—nearly seven years without a reboot!

Stories like this weren't unusual. Sysadmins often shared these stories like campfire tales:

> "After 3,737 days of continuous operation, we finally shut down our trusty Sun 280R server running Solaris 9. It hadn't been used for a while, but we made a [tribute video](https://www.youtube.com/watch?v=fAUvfqLEWuA) to honor its long service and retirement."

{{< youtube fAUvfqLEWuA >}}

The mindset then was simple: _“If it isn't broken, don't fix it.”_ Security updates and patches often seemed less important, especially if a server had proven itself reliable.

But times have changed.

### A Different Time for Servers

Back in the 90s and early 2000s, rebooting a server was a big deal. It wasn't a regular maintenance task; it was something you only did if absolutely necessary. Most servers had unique names and personalities and were personally installed by the admins themselves. This might sound funny nowadays, but there where a good reasons for this.

#### Fear of Downtime

Rebooting a server meant downtime, which businesses and users hated. High availability technologies like load balancers were still new, so downtime was unavoidable. Reboots weren’t quick—they could take several stressful minutes if not hours.

#### A Pride of Uptime

Back then, uptime was a badge of honor. Rebooting was the absolute last resort. Sysadmins would do everything possible—applying temporary fixes, patching configurations manually, or even tolerating minor issues—just to avoid restarting. Successfully avoiding a reboot was a point of pride, celebrated among peers as proof of expertise and resourcefulness.

#### Boot Problems

Older hardware and software weren't as stable as they are today. A reboot could cause unexpected issues, like configuration errors or hardware failures. Sysadmins often worried if their servers would even restart properly.

#### Limited Remote Management

Remote management wasn't common back then. If something went wrong during a reboot, if no remote hands where available, sysadmins often had to physically visit the data center to fix it, lugging around carts loaded with screens and keyboards. I've biked, I drove but also had to take taxi.

#### Valuing Stability

Sysadmins strongly believed, "If it isn't broken, don't fix it." Stability was more important than frequent updates.

#### No Easy Updates

Unlike today, older servers didn't support live updates. Updates typically required a full reboot, and maintenance windows were carefully planned and dreaded.

### Today: Uptime Isn't Everything

Now, long uptimes can actually signal problems. Tools like Ansible and Puppet prioritize regular updates and security over long-running servers.

Still, it's fun to think about those older servers that kept running, year after year, simply because no one dared to turn them off. Those days had their own kind of charm—even if they weren't the safest.

> Discalmer
> This is application point of view. To this date, there are significant number of devices - which are not servers, that require high uptime for stability. In example, many core and edge network devices, despite having redunancy is prefered to be stable.
