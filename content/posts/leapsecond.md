---
title: "CLOCK: INSERTING LEAP SECOND 23:59:60 UTC"
date: 2009-09-27T22:36:36+01:00
draft: false
---

I found above message in a kernel log and I was just wondering what is all about. I was amazed how little I knew about it.

> A leap second is a second, as measured by an atomic clock, added to or subtracted from UTC to make it agree with astronomical time to within 0.9 second. It compensates for slowing in the Earth’s rotation and is added during the end of June or December. The first leap second was added to atomic clocks in 1972, with the most recent leap second being added on December 31, 2008.

There is a organization called [International Earth Rotation and Reference Systems Service](https://www.iers.org/) that it’s making decision to introduce a leap second. You can find announcement of last leap second.

There have been many discussions and proposals for and against the future of leap seconds. A vote to stop leap seconds is currently being planned. NTP deals quite well with leap seconds.


And finally ‘**Clock: inserting leap second**’ is found in kernel/time/ntp.c:143 of the 2.6.28 linux kernel.

### Well then you would think why is the Earth Slowing Down?

According to Donald L Hamilton, author of “The Mind of Mankind” (cited in “On second thought” in the Cape May County Herald), the Earth loses its kinetic energy due to all forms of friction acting on it; tides, galactic space dust, solar wind, space weather, and geo-magnetic storms.
