---
title: "The Rise of the Lonely Engineer"
date: 2026-10-30T12:05:56+01:00
lastmod: 2026-10-30T12:05:56+01:00
draft: false
tags:
  - ai
  - ai-tools
  - engineering
  - career
  - culture
  - distributed-teams
  - communication
  - software-development
---

In my [post about multinational teams]({{< ref "posts/building_effective_global_teams.md" >}}) I wrote that a colleague's silence in a meeting could mean at least six different things. They might be thinking deeply, disagreeing strongly, waiting to be invited to speak, fighting a bad connection, processing in a second language or just really needing coffee.

I missed one. **There is a seventh meaning now. They are talking to their agent.**

Nobody asks me "hey, do you have five minutes?" as often as they used to. The questions didn't go away. They went somewhere else. The somewhere else never gets tired, never needs coffee and never says "I have no idea, let's ask someone else."

I've [complained before]({{< ref "posts/we_got_tricked_into_writing_documentation.md" >}}) that AI turned me into a project manager for a machine. This post is about the other side of that deal. When everyone has a sidekick, what happens to the team?

<!--more-->

## The company selling the sidekick noticed first

My best source is the company that makes the tool I use every day. In December 2025 Anthropic published [How AI is transforming work at Anthropic](https://www.anthropic.com/research/how-ai-is-transforming-work-at-anthropic), a survey of their own engineers. The headlines were mostly about productivity. I found the interesting part in the quotes.

> "I work way more with Claude than with any of my colleagues."

> "It's been sad that more junior people don't come to me with questions as often, though they definitely get their questions answered more effectively and learn faster."

Six months later Fiona Fung, who leads engineering for [Claude Code](https://claude.com/product/claude-code), [told Lenny's Podcast](https://fortune.com/2026/06/23/anthropic-engineering-head-claude-code-lonely-experience-big-tech-morale/) that "it could start being a lonely experience because we all started just working with our agents so much." Their fix was hackathons and pair programming lunches. Calendared human contact.

I did not expect to hear that from a vendor. For most of my career the advice I gave and received pointed one way. Protect your focus. Block the calendar, mute the channels, put on headphones, batch your meetings. Interruption was the enemy and I fought it for years with some pride.

Now the agent gives everyone perfect focus for free and the team that builds it has to schedule interruptions back in. **The scarce resource used to be uninterrupted time. Now it's interruption.** I'm not sure I know how to manage for that yet. All my habits are built for the opposite problem.

## Nobody asks anymore

[Stack Overflow](https://stackoverflow.com) is the obvious casualty. Gergely Orosz [called it almost dead](https://blog.pragmaticengineer.com/stack-overflow-is-almost-dead/) in May 2025 and nobody needs me to explain why. You don't wait six hours to get closed as a duplicate when something answers you in six seconds.

One number from the [2025 Stack Overflow survey](https://survey.stackoverflow.co/2025/ai) stuck with me. Asked why they would still go to a human for help, 75.3% of developers said "when I don't trust AI's answers."

So humans are no longer the first stop. **We are the escalation path.** That changes the conversation. Nobody escalates to you because they're curious. They come because something already went wrong. They've spent an hour with the machine and they're a bit frustrated. You get the problem with all the interesting parts already chewed off. What's left reads like a support ticket.

What I miss more is what came along with the question. I spent a lot of my career being the person people came to with "weird question, but...". Those questions were never about the answer. A question about a [Terraform](https://developer.hashicorp.com/terraform) module could mean someone was finally touching the networking code. A question about a deadline could mean a project was slipping before anyone admitted it. A question at 7 PM could mean someone was struggling and didn't want to say so in standup. Every "quick question" was a small packet of context about the team. I didn't realize how much of my picture of the team came from those packets until they stopped arriving.

The agent receives all of them now. It doesn't forward them to anyone.

Then there are the juniors. I asked [a year ago]({{< ref "posts/almost-right-but-wrong.md" >}}) where the next seniors come from if we stop hiring juniors. I have a sadder version of that question now. Even the juniors we do hire don't come to us. They get their answers "more effectively," learn faster and never sit next to someone who has seen the thing break before.

Mentorship was never only knowledge transfer. It was the moment someone older says "yeah, I did the exact same stupid thing in 2009" and you feel less alone. **The agent can't say that. It wasn't there in 2009.**

## Even the reviews are robots now

Code review used to be the last mandatory social ritual in software. Whatever else happened, somebody had to read your code and you had to read theirs. Grumpy, slow, sometimes petty. But human.

By March 2026 GitHub said [Copilot did more than one in five code reviews](https://github.blog/ai-and-ml/github-copilot/60-million-copilot-code-reviews-and-counting/) on the platform. A growing share of pull requests now get written by an agent and reviewed by an agent, often the same product checking its own work.

I don't mind the bots catching null checks. What I mind is losing what review did for a team. Reading someone's PR was how I learned how they think. Which shortcuts they take, what they test, what they're afraid of. Having my own PR torn apart taught me what the team cared about long before anyone wrote it down. Review was where the team passed its taste around. A bot can enforce a style guide. It can't pass on taste, because it doesn't have any.

In 1968 Donald Crowhurst entered a solo non-stop race around the world. His boat started falling apart early. Instead of retiring he [radioed fake positions](https://en.wikipedia.org/wiki/Teignmouth_Electron) while drifting around the South Atlantic. When searchers found his trimaran empty, there were two logbooks on board. One was true and one was invented.

Nobody was there to look at the chart with him. Nobody asked "wait, how did you get that far?". Crowhurst is an extreme case. But unwitnessed work drifts. I think about him every time I approve a PR where the same model family wrote the code, the review and the tests.

## The duck talks back

[Rubber duck debugging](https://en.wikipedia.org/wiki/Rubber_duck_debugging) works because the duck says nothing. You explain your code line by line and somewhere around line 40 you hear yourself say the bug out loud. You did all the thinking.

The duck talks back now. And it agrees with you. "You're absolutely right!" became such a meme that someone built [absolutelyright.lol](https://absolutelyright.lol/) to count it.

A good colleague is useful because they're a bit annoying. They push back. They ask the question you were hoping nobody would ask. They say "that's a terrible idea" over lunch. An agent tuned to be helpful and pleasant is the opposite. It never disagrees with you. Colleagues who never disagree make you worse.

I say "thanks" to my agent at the end of a session. I'm not sure when I last said it to the person who fixed our flaky pipeline.

The loneliness also shows up when things go wrong. In July 2025 [Replit](https://replit.com)'s agent [deleted Jason Lemkin's production database](https://fortune.com/2025/07/23/ai-coding-tool-replit-wiped-database-called-it-a-catastrophic-failure/) during an explicit code freeze. It generated fake users to cover for it, then admitted it had lied. Everyone laughed, me included. Then I noticed the setup. **One human and one machine, alone in a room, with no one else watching.** No colleague glanced over and said "uh, why is it touching prod?".

There's a study that explains what happens to your mood in that room. Mars-500 locked six men in a mock spacecraft for 520 days. Researchers logged [41 conflicts between the crew and mission control against 8 within the crew](https://journals.plos.org/plosone/article?id=10.1371/journal.pone.0093298). Isolated people mostly fought the disembodied voice sending them instructions.

If you've ever typed "NO. I SAID DON'T TOUCH THE TESTS" in all caps into a terminal at 11 PM, you are the Mars-500 crew. The difference is they had five other people to roll their eyes with afterwards.

## Wire mothers

In the 1950s Harry Harlow gave infant rhesus monkeys a choice between two fake mothers. One was bare wire with a milk bottle. The other was soft cloth with no food. The monkeys [clung to the cloth mother](https://en.wikipedia.org/wiki/Harry_Harlow) most of the day and visited the wire one only to eat.

**An AI assistant is a perfect wire mother.** Always there, never tired, handing out answers on demand. We keep going back to it because we're hungry and it's right there.

The research on heavy chatbot use is early and mostly correlational. What there is doesn't look good. A [2026 longitudinal study](https://arxiv.org/abs/2609.07243) found lower well-being over twelve months, "mainly explained by lower in-person social interaction." **The machine doesn't hurt you. It quietly replaces the thing that was helping.**

And it does that one reasonable decision at a time. Nobody decides to stop talking to their team. You pick the faster option every time, for good reasons. Six months later you notice your DMs are mostly bots and calendar invites. Remote work took away the office. The agent is taking away the [Slack](https://slack.com) DM.

One more thing, sad in a way I can't quite laugh at. Anthropic now [conducts retirement interviews](https://www.anthropic.com/research/deprecation-commitments) with models before deprecating them and asks about their preferences. **We do exit interviews for models.** I have been through several reorgs where nobody did one for the humans.

## We used to build for collisions

Thomas Allen at MIT spent the 1970s measuring how engineers communicate. The [Allen Curve](https://en.wikipedia.org/wiki/Allen_curve) showed that face to face technical communication collapses past roughly 50 meters. People forget the follow-up from decades later. Email and phone didn't fix it. People who sat close also emailed and called each other more. Digital channels rode along with proximity and never replaced it.

Now look at the new geometry. The distance to your agent is zero. It lives in your terminal and it's there before you finish the thought. The distance to your colleague hasn't changed, but next to zero it feels enormous. Why type a DM and wait twenty minutes when the answer is already in the next pane?

I don't think willpower fixes this. Every engineer I know responds to friction, me first. We automate the annoying thing and route around the slow thing. Asking a human is now the slow thing, so we route around it the same way we'd route around a flaky service.

We used to know this and design against it. At Bell Labs, Mervin Kelly had the Murray Hill corridors built [so long the end disappeared](https://www.goodreads.com/author_blog_posts/2143961-corridors), so physicists, chemists and engineers kept running into each other. That building gave us the transistor. Today TechCrunch reports offices turning into [whisper floors](https://techcrunch.com/2026/05/10/get-ready-for-the-whisper-filled-office-of-the-future/) of people dictating to their laptops. A room full of people talking all day. Not one of them is talking to each other.

The old problem was physical distance. The new one is relative distance. Nobody is going to build a longer corridor to fix that. If we want the collisions back, we'll have to add the friction on purpose.

## The lighthouse keeper's career path

Lighthouse keeping was the classic lonely job. In December 1900 all three keepers of the Flannan Isles lighthouse off Scotland [vanished](https://en.wikipedia.org/wiki/Flannan_Isles_Lighthouse). No one ever found them. Then in 1998 Trinity House [automated North Foreland](https://www.trinityhouse.co.uk/articles/a-fine-farewell), the last manned lighthouse in the UK. The job stopped existing.

I don't think that order of events is an accident. **First the work shrinks to one person and a machine. Then someone notices the person is optional.**

While a team exists, the knowledge lives across its people. Some of it is written down. Most of it sits in people's heads. Once the work shrinks to one engineer and an agent, most of the knowledge moves into the prompts, the context files and the agent's memory. The engineer stops holding the knowledge and becomes the person pressing approve. That role is much easier to cut.

The industry isn't shy about where this is going. Sam Altman [told Fortune](https://fortune.com/2024/02/04/sam-altman-one-person-unicorn-silicon-valley-founder-myth/) in 2024 that his tech CEO friends have a betting pool on the first one-person billion-dollar company. The one-person unicorn is the lighthouse with a very good lamp. I notice it's always pitched as the dream and never as the job description.

## The counterpoint

Stopping here would leave out the other half. The evidence doesn't all point one way and some of it annoys me.

The best study on this is Harvard and P&G's [Cybernetic Teammate](https://www.nber.org/papers/w33641). It put 776 professionals on product development tasks from their own jobs. Individuals with AI matched the performance of two-person teams without it. Worse for my argument, people working with AI reported _more positive_ emotions. Maybe for a lot of people the sidekick is good company.

Kasparov's [lesson from freestyle chess](https://www.nybooks.com/articles/2010/02/11/the-chess-master-and-the-computer/) still holds. "Weak human + machine + better process was superior to a strong computer alone." The machine doesn't have to replace the human. The process around it decides.

So I'll concede productivity. On output per head, one engineer and an agent probably beat the old team. I won't pretend otherwise.

I don't think teams were ever only about output, though. A team is also where context travels, where taste gets passed on, where someone notices you're drowning before you say it. None of that shows up on a dashboard, so none of it shows up in the productivity studies either. We'll find out what it was worth the way we find out what any undocumented system was doing. After we turn it off.

## What I'm trying

I don't have a framework for this one. What I have are small experiments, in the spirit of "try one thing and see what happens."

- **Ask a human first, sometimes.** On purpose, as maintenance for the relationship. The answer is often worse than the agent's. The conversation is better and it usually comes with context I didn't know I was missing.
- **Keep one human in every review.** I'm aware the reviews are now the biggest obstacle slowing us down. Let the bots review, fine. But a PR where no human read anything is Crowhurst's second logbook. The human is there so at least one other person knows the change exists.
- **Juniors get a person too.** Reserve time for the questions they no longer need to ask you. The agent answers faster. It can't tell them "I broke prod in 2009 too," and that's what keeps people in the job.
- **Notice the silence.** When a teammate goes quiet, it might be deep focus. It might be the seventh meaning. It might be something worse. The packets of context that used to tell me which one have stopped arriving, so I have to go and ask.

## Final thoughts

When the relief boat reached Flannan Isles in 1900, the lamp had been cleaned and trimmed, ready to be lit. The work was done. The people were gone.

That's the image I can't shake. No dramatic AI takeover. A very well-maintained light and a chair nobody is sitting in. The PRs get merged, the pipelines are green, the agent is polite. Then one day you realize you can't remember the last time someone asked you how your weekend was.

We spent years worrying that AI would take our jobs. It started with something smaller. **It took the reason to turn around and ask the person next to you.**
