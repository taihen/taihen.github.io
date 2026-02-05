---
title: "Working Effectively in Multinational Teams as an Engineer"
date: "2026-02-05T19:00:00+02:00"
draft: false
tags: ["distributed-teams", "culture", "communication", "engineering"]
---

Early in my career, I assumed everyone worked the same way I did. I thought
"professional" meant universal and that technical rigor transcended culture. I
was not just incorrect, **I was naively wrong**.

I've spent about twenty-five years working in infrastructure engineering, almost
always in multinational distributed teams. I've been the formal manager of an
infrastructure team twice in my career, but mostly I've been on the other side,
a team member trying to figure out how to work with people who think,
communicate, and collaborate completely differently than I do. Right now, I'm in
a team of seven people where every single person has a different nationality,
trying to help, enable and influence while "my way" might not be "their way." I
misjudge people's preferences regularly and have to repair trust after the fact.
It's uncomfortable every time, but it's part of the process.

Along the way, I discovered two frameworks that gave me language for patterns
I'd been bumping into for decades. [Erin Meyer](https://erinmeyer.com/)'s [The
Culture Map](https://erinmeyer.com/books/the-culture-map/) explains why people
communicate, decide and trust differently across cultures. [Matthew
Skelton](https://matthewskelton.net/) and [Manuel
Pais](https://manuelpais.net/)'s [Team Topologies](https://teamtopologies.com/)
explains how to structure teams and their interactions to reduce friction.
Neither is sufficient alone. Team Topologies gives you structural patterns but
assumes cultural homogeneity, while The Culture Map gives you cultural awareness
but doesn't tell you how to organize teams. Together, they transformed how I
think about global team design.

## Why Good Intentions Are Not Enough

I remember a project where I was coordinating between teams in the Netherlands,
India and the US. We had a tight deadline for a migration and during our sync
calls, I kept hearing "yes, we'll handle it" from one of the teams. Two days
before the cutover, I discovered they'd barely started. When I asked what
happened, the answer was careful and indirect: they'd been trying to signal for
weeks that the timeline was impossible, but they did it by saying things like
"this will be challenging" and "we'll do our best."

> I'd interpreted that as normal engineer caution. They'd interpreted my pushing
> forward as me ignoring their concerns. The migration got delayed,
> relationships were strained and I learned that what counts as "clear
> communication" varies dramatically.

This is what Meyer calls the difference between **low-context** and
**high-context** communication. Low-context cultures (US, Netherlands, Germany)
value explicit, precise language - basically you say exactly what you mean.
High-context cultures (Japan, India, Middle East) rely heavily on implicit
understanding and reading between the lines. When I asked "can you deploy this
by Friday?" and heard "I'll try my best," they were signaling that it was
impossible. I heard normal engineering caution. Neither of us was wrong. We were
operating on completely different assumptions about **what counts as clear
communication**.

I still fall into this trap sometimes, but I recover faster now because I
recognize the pattern: **silence doesn't always mean agreement, enthusiasm
doesn't always mean commitment** and "yes" doesn't always mean "yes, I can do
that."

## Making the Invisible Explicit

Here's something I learned the hard way: a colleague's **silence in a meeting
could mean at least six different things** depending on where they're from and
how they're wired. They might be thinking deeply, or disagreeing strongly, or
waiting to be explicitly invited to speak, or dealing with a terrible internet
connection, or processing in a second language, or just really needing coffee.
"Maybe we should reconsider this approach" could mean "this is a terrible idea
that will set us back months" or "I have a tiny concern about edge case
handling."

These days, I **note main decisions** and next steps after every meeting. I do
it because it's amazing how often people say "wait, that's not what I thought we
decided."

I've also gotten into the habit of **asking clarifying questions** that feel
almost comically obvious. When someone says a task will be "challenging," I'll
ask them to help me understand if they mean it's technically risky but doable,
or if they're trying to politely tell me the timeline is fantasy. When we agree
on something, I'll ask people to walk me through how they're planning to
approach it, which surfaces misalignments fast and saves everyone from
discovering three weeks later that we were building completely different things.

This connects to something Team Topologies calls **cognitive load**, the mental
effort required to do your job effectively. They identify three types:
**intrinsic** (the fundamental complexity of the problem), **extraneous**
(overhead from tools, processes and unclear boundaries) and **germane** (effort
needed for learning and growth). For multinational teams, I'd add a fourth type:
**cultural** cognitive load. Working in a second language, navigating unfamiliar
social norms, constantly code-switching between cultural contexts—it's
exhausting. Every unclear communication, every meeting where you're not sure if
people actually agreed, compounds that burden. Making the implicit explicit
isn't just good practice, it's how you keep your team from burning out on
overhead instead of doing actual work.

## Feedback, Disagreements and Trust

I once gave what I thought was straightforward technical feedback in a design
review, pointed out an unfeasible condition in someone's proposal, suggested an
alternative approach, moved on. The person didn't say much during the rest of
the meeting. Two days later, a colleague pulled me aside to let me know I'd
essentially shredded this person in public from their perspective and they were
now convinced I thought they were incompetent.

> I'd been going for "here's a technical issue we should fix" and they'd heard
> "you're bad at your job."

Meyer calls this the evaluating dimension, **direct** versus **indirect**
**negative feedback.** Netherlands, Germany and Russia tend toward brutal
honesty. Japan, Thailand and many Latin American cultures wrap negative feedback
in layers of positive framing. Telling a German colleague "this architecture has
serious problems with X, Y and Z" is received as helpful directness. Saying the
same thing to someone from a culture that values indirect feedback can feel like
a personal attack, even when you're only discussing technical issues.

> Different people are comfortable with wildly different levels of directness.

I've worked with teammates who expect blunt, public technical criticism and will
happily tell you when you're wrong in front of everyone. I've worked with others
who need written feedback in a private channel and time to process before they
can discuss it. Both approaches are valid. The disaster happens when you treat
everyone the same way and assume your default is universal.

These days, I try to offer both **written** and **one-on-one** feedback
channels, because some people process criticism better when they can read it
twice and think about it, while others need the nuance of a live conversation to
know you're not attacking them. I've also learned to frame criticism around the
work itself rather than the person who did it. Saying "this approach has a race
condition under high load" lands differently than "you didn't think about
concurrency," even though both sentences point at the same technical problem.

While working as a Principal Engineer, a junior engineer caught a significant
error in my proposal. I made sure to **highlight** it in the team retrospective
later, not to praise them for being smart, but to demonstrate that disagreeing
with me led to a better outcome for everyone. If people think disagreement is
dangerous, they stop doing it and then you're just building things wrong
together.

## Tools Don't Fix Teams

I've watched organizations assume that giving everyone Slack and Zoom solves
distributed work. **It doesn't**. Without intentional structural design, you end
up with functional silos recreated digitally, those #ask-engineering channels
that become black holes where requests go to die. Tools don't fix structure.
Structure informed by both team design principles and cultural awareness does.

[Team Topologies](https://teamtopologies.com/) gave me language for what I'd
been figuring out the hard way. The part that matters most for multinational
work: how teams interact. And for global teams, I've found
[X-as-a-Service](https://teamtopologies.com/news-blogs-newsletters/x-as-a-service)
is gold—one team provides a service that others consume with minimal
coordination.

When a platform team provides **well-documented**, self-service capabilities,
you dramatically reduce the need for synchronous communication across cultures
and time zones. Instead of scheduling meetings where direct German feedback
might clash with indirect Japanese communication styles, teams consume services
through clear APIs and documentation. The interface becomes the translator.

There's a related concept called the [**Thinnest Viable
Platform**](https://teamtopologies.com/key-concepts-content/what-is-a-thinnest-viable-platform-tvp),
the smallest set of APIs, documentation and tools needed to accelerate other
teams. For global organizations, this prevents platform teams from building
elaborate solutions that require extensive training and synchronous support.

> Start with documentation.

A well-maintained wiki can be your TVP. It's asynchronous, searchable and
doesn't care what time zone you're in. Add self-service capabilities gradually
based on what teams actually need, not what headquarters assumes they need.

I also use what Team Topologies calls [**Team
APIs**](https://teamtopologies.com/key-concepts-content/category/Team+API),
explicitly defining what a team provides, how to interact with them and what
they expect. I include cultural working preferences: preferred communication
channels and expected response times, how the team prefers to receive feedback,
decision-making process (do they need consensus or can the lead decide?) and
meeting norms (cameras on/off?, how do we handle questions or interruptions?).
Making these explicit prevents the collision between different cultural
defaults.

## Decision Making

Meyer's framework distinguishes **consensual** cultures (where everyone needs to
agree before moving forward) from **top-down** cultures (where someone makes the
call, often quickly). Germany and Netherlands are surprisingly consensual while
US and France are more top-down.

> The trap: consensual decision-making looks slow to top-down cultures and
> top-down decisions feel reckless to consensual cultures.

I'm Eastern European and like to move fast, my first year in the Netherlands was
hard as I did not understand why my Dutch colleagues wanted to have a discussion
about pretty much... anything. When I tried to "move fast" it destroyed team
trust. **They felt steamrolled and I felt blocked and disappointed**.

Now I make decision processes explicit before each significant decision. Who has
input? Who makes the final call? When will we decide? With consensus-oriented
teammates, I build in discussion time and explicitly work toward alignment. With
top-down teammates, I make clear who has authority and when the decision will be
made. **I don't assume my approach is universal**.

Trust compounds this complexity. Task-based cultures (US, Netherlands, Germany)
build trust through reliability and competence:

> "I trust you because you deliver quality work."

Relationship-based cultures (China, India, Middle East, Latin America) build
trust through personal connection:

> "I trust you because I know you as a person."

This affects everything. In relationship-based cultures, jumping straight to
business without personal connection feels cold and transactional. In task-based
cultures, spending time on personal chat before getting to work can feel like a
waste.

In remote multinational teams, you need to create opportunities for both.
Virtual coffee chats, team offsites when possible and non-work Slack channels
aren't fluff, they're trust infrastructure for half your team. But you also need
to **deliver reliably and follow through on commitments**, because that's trust
infrastructure for the other half.

## What Being a Manager Taught Me About Being a Team Member

Most of my career, I've been an infrastructure engineer without any formal
authority. I propose architectural changes, coordinate across teams, mentor
people and generally try to influence things through expertise and
relationships. It's a comfortable spot, you get to have opinions about how
things should work without being the person everyone blames when they don't.

I've been the formal manager of an infrastructure team twice and both times were
harder than I expected. The first time, I optimized almost entirely for
technical delivery and somehow didn't notice that the team was falling apart
until someone quit. The second time, I over-indexed on process and structure to
the point where I was slowing everyone down with meetings and checkpoints that
made sense in my head but drove them crazy in practice.

> Both times I thought I understood what I was doing and both times the team
> taught me I didn't.

The weird thing is that being a manager and being mediocre at it, made me better
at being a team member. I learned that decision processes need to be explicit,
not just understood by whoever happens to be in charge. When everyone knows who
has input, who actually decides and when we'll revisit the decision, you avoid
the "I thought you were handling it" disaster that happens way too often in
distributed teams.

I also learned that **protecting focus time is critical**. Infrastructure work
requires deep concentration and in a global team someone is always online and
someone is always asking questions. Normalizing "I'm blocking three hours on my
calendar and won't respond during that time" turned out to be a gift to
everyone, not just me.

These days, I'm back to being a team member and honestly I'm more useful here
than I was in management. But I'm grateful for those two painful stints because
they gave me perspective I wouldn't have gotten otherwise.

## What Changed in Me

The biggest shift over twenty-five years hasn't been technical, it's been
realizing that "my way is normal" is one of the most expensive assumptions you
can make. When I started, I thought professionalism was a universal language and
that good engineers everywhere approached problems the same way. Then I spent
two decades working with people from every continent and learned that almost
nothing about how I work is universal. The way I communicate, give feedback,
make decisions, handle conflict, structure my day, process information, all of
it is cultural and personal, not fundamental.

> The most effective teams I've seen aren't the ones where everyone thinks alike
> or where the loudest person wins. They're the teams that share context openly,
> stay genuinely curious about why their teammates work differently and design
> their processes deliberately instead of letting them emerge by accident and
> then wondering why everything is chaos.

I'm writing this from my current team of seven... seven nationalities, all
working around a single hub except for my manager who's in a different time
zone. Some days it's exhausting. Some days it's the best part of the job. Most
days it's both. Is it a well-oiled machine? Not at all. We still have plenty of
things to figure out.

## TL;DR

If you're suddenly in a very diverse team or stepping into your first
infrastructure management role, start by knowing people, how they prefer to
receive feedback and actually listen to the answer. Don't assume your default is
everyone's default, because it isn't. Pick one process that's currently
implicit, i.e., how you handle incidents, how design decisions get made, how you
prioritize work and write it down. You'll discover misalignments you didn't know
existed and half your team will be relieved that someone finally said it out
loud.

When something goes wrong and it will, assume good intent and ask what systemic
issue made it easy to fail. Culture gaps are almost always system problems, not
people problems. Fixing the system is more useful than fixing the person.

The goal isn't to eliminate cultural differences, it's to create structures that
let diverse teams thrive while reducing the friction that comes from unexamined
assumptions. Start by mapping your team's cultural profiles. Design your team
structures, interaction modes and communication norms with those profiles in
mind. Make everything explicit. Default to async, that's a way to go.

Treat all of this as input, not a template. What works for one team might be
completely wrong for yours. Run your own experiments. In your next incident
review or design meeting, try one small change and see what happens. Pay
attention to what works and what doesn't. That's how you learn what fits your
team, in your context, right now.
