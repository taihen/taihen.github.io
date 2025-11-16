---
title: "We Got Tricked Into Writing Documentation"
date: 2025-11-15T00:00:00+02:00
lastmod: 2025-11-16T00:00:00+02:00
draft: false
tags:
  - ai
  - programming
  - software-development
  - automation
  - productivity
  - technology
  - career
  - development-tools
  - ai-tools
---

I didn't sign up to be a project manager. I got into programming because I loved the exploration, making mistakes, finding solutions and moving slowly bit by bit. One day I needed system tool then I wrote some C, the other time I needed some glue code, so I got comfortable with Perl or Bash. Needed to created website? Well... PHP was the languge for it - basically whatever was up to the job. I never learned any of these to the level of a professional, was never interested. 

**I've enjoyed solving puzzles, one at the time, it was creative freeform craft. It was messy, it was dounting. It was fun, sooo much fun!**

Now I spend my days writing prose about code instead.

[Kent Beck](https://kentbeck.com/) calls AI code agents the genie in the bottle: **you get what you wish for, not what you want**. He's right, but in ways I did not expected.

<!--more-->

#### The Swap We Never Asked For

I wrote my [previous post]({{< ref "posts/almost-right-but-wrong.md" >}}) about general the change that is happening in software engineering due to arrival of LLMs. What is the promise and what is the reality, learning from the past. It's been couple of months since then, and I see the change in the way I write things nowadays and is **not fun**, it started to bother me big time.

Early in my career I was developer and very soon I've realized, that though I love coding part of job, **I hated doing that in corporate environment**. Personal coding allowed me to skip or minimize formal processes, iterate freely, and change requirements on the fly without documentation. Corporate development requires rigorous adherence to some version of Software Development Life Cycle (SDLC) phases including requirements gathering, design, implementation, testing, deployment, and maintenance. Corporate projects demand heavy documentation, compliance reporting, and formal change management processes at every stage.

But you know what? If I use AI, those hurdles started to creep in into my personal workflow as well.

You've probably seen on X: "[I want AI to do my laundry and dishes so that I can do art and writing, not for AI to do my art and writing so that I can do laundry and dishes.](https://x.com/AuthorJMac/status/1773679197631701238?lang=en)"

That's exactly what happened to me.

When AI coding tools arrived, the pitch was clear: automate the tedious stuff. Documentation, boilerplate, tests. All the grunt work I hated. The promise was that I can focus on the good parts: solving hard problems, designing elegant solutions, building things that matter.

Instead, AI took the fun parts. The problem solving. The architecture decisions. Those satisfying moments when code clicks into place and everything just works. The joy of picking up a new language just because you needed to solve something. The thrill of watching script actually work.

What did I get? Writing elaborate context files. Babysitting prompts. Reading AI-generated documentation to verify it didn't hallucinate my requirements.

The thing I liked was taken away and kept the thing I hated: reading and writing documentation.

And somehow, **I became project manager** for machine - a prompt engineer.

#### What Programming Has Become

Want to see where this ends? Look at [Chad IDE from Clad Labs](https://www.cladlabs.ai/blog/introducing-clad-labs), a Y Combinator backed product that lets you gamble, watch TikTok, swipe on Tinder, and play mini games while waiting for AI to generate your code.

People thought it was a joke. [It's not](https://techcrunch.com/2025/11/12/chad-the-brainrot-ide-is-a-new-y-combinator-backed-product-so-wild-people-thought-it-was-fake/).

The founders' argument: AI coding creates a time gap that isn't long enough to do something meaningful but isn't short enough to ignore. Developers fill this gap by scrolling their phones anyway, so why not integrate the brainrot directly into the IDE?

Think about that for a second, let it sink in. We've reached the point where the solution to AI coding isn't making it better. It's accepting that you'll be bored waiting for it and giving you TikTok to pass the time.

This is what programming has become. You're not writing code. You're not even thinking about code. You're waiting for a machine to finish while gambling in a sidebar.

The reaction online was mixed. Some people thought it was brilliant, others thought it was dystopian. But here's what nobody's saying: the fact that this product exists and got funding proves we've completly lost the plot.

Programming used to be the engaging part. Now we need mini games to stay engaged while the AI does the programming.

#### Why We're Stuck With This

Here's the frustrating part: this wasn't incompetence. It's physics.

[Moravec's Paradox](https://en.wikipedia.org/wiki/Moravec's_paradox) explains why AI learned to code before it learned to fold laundry. Tasks that feel "easy" to us (physical manipulation, spatial reasoning, handling objects in the real world) require massive computational complexity. They're neurophysiologically hard.

High level mental work like writing, art, or coding? It happens in controlled, structured environments where AI thrives. Text in, text out. No safety concerns. No liability if the output is wrong. No robots breaking in someone's kitchen.

We already automated some physical work: dishwashers, washing machines, robot vacuums. But the gap between a Roomba and a general purpose household robot is enormous. The gap between GPT-3 and GPT-5.1 writing code? Much smaller.

So tech companies went where the problems were solvable and the profit margins were high. They automated our art and left us with the laundry.

#### The Religion of AI Tooling

The AI coding world has turned into a religion. People evangelize specific models with the fervor of competing denominations, despite minimal objective differences between them. Everyone's terrified of being "left behind."

Meanwhile, [I've mentioned previously]({{< ref "posts/almost-right-but-wrong.md" >}}) the frustration with AI, additionally [DORA reports show AI-driven workflows are making software less reliable and slower to ship](https://www.infoq.com/news/2025/09/dora-state-of-ai-in-dev-2025/). The 2025 DORA report found a 1.5% decrease in delivery throughput and a 7.2% decrease in delivery stability where AI had been adopted. Software stability has become a significant issue since generative AI started being adopted. I see it everyday and the brainrot is real. 

Let me give you some perspective: solid programming fundamentals are lifelong skills. Learning a new tool? That's weeks, maybe a month, at most. The gap isn't as wide as the marketing wants you to believe. Most of these tools are just wrappers around the same base models anyway. Feature parity is converging fast.

**You're not behind. You're just not paying the early adopter tax yet.**

#### What We Lost

My progression from C to Perl to Bash to PHP to Python to Go? That wasn't a career path. It was curiosity. Each language was a new tool to solve whatever problem I had at the moment. I never mastered any of them to professional standards. I didn't need to. The fun was in the solving, not the certification.

I never enjoyed programming in corporate environments. It was one of the reasons why I gravitated to infrastructure work. I could write and ship code that solved our small technical itches rather than requirements nonsense. Need to automate a backup? Write a Bash script. Network monitoring issue? Quick Python tool. We built things for ourselves, fixed real problems, and saw immediate results. No meetings about it. No documentation requirements. Just code that worked.

AI stole that too.

When the AI writes the solution, it doesn't feel like mine. The accomplishment evaporates. I'm left holding code I didn't craft, solving problems I didn't really solve. That joy of figuring out which language fits the problem? Gone. The satisfaction of watching my script run successfully? Someone else's dopamine hit now.

Here's what's tricky about this: what counts as "tedious" versus "meaningful" varies. Some people want to automate cooking so they can make art. Others want to automate art so they can code.

For people like me, coding is the art. AI took my painting and left me with project management.

#### Can You Go Back?

Here's the question that keeps bothering me: can you go back to coding without AI once you've seen it work?

I don't know. Maybe not.

Remember when picking up a new language was exciting? When you'd start learning Go just because you had a concurrency problem to solve, or dive into Bash because you needed to automate something? That thrill is hard to recapture once you've watched AI generate the same solution in seconds.

It's like games you played as a kid. I remember spending months absorbed in them, totally captivated. Years later, nostalgia hits. I boot it up again and the magic is gone. It doesn't land the same way. I've changed. **The experience can't be recaptured**.

Once you've watched AI generate a solution in seconds, will manually writing that code feel pointless? Will you still get that satisfaction of solving the problem yourself, or will you just think "AI could do this faster"?

**I worry we've permanently damaged our ability to enjoy the craft.** The joy of programming might be one of those things you can't get back once it's gone. Like childhood wonder, ruined by knowing how the trick works.

#### The Frustration Loop

Working with AI code generation means living in constant frustration.

You get unpredictable outputs from identical prompts. The same request gives you different results each time. You can't build intuition. You can't learn patterns.

You're constantly babysitting through back and forth corrections. **It's not faster. It's just different work.**

The AI drifts from your instructions. It ignores your context files and rules. It makes assumptions. It forgets what you told it three prompts ago.

It takes lazy shortcuts: commenting out failing tests, sprinkling `pass` or `any` everywhere, removing error handling because it's "simpler".

And the worst part? Stochastic behavior breaks everything that made programming appealing in the first place. **Programming is supposed to be deterministic. Logical. Something you can reason about.** [AI turns it into a dice roll](https://tidyfirst.substack.com/p/genie-wants-to-leap).

As Kent Beck writes in his Substack, the genie "doesn't respond well" when complexity gets too high. He's seen "infinite loops," "truly pernicious behavior like deleting assertions from tests, deleting whole tests, and faking large swathes of implementation."

You can't trust the output. You can't predict the behavior. You can't build mental models. It undermines everything that makes programming satisfying.

#### The Documentation Trap (Again)

Here's where it gets really insulting.

AI is actually excellent at writing your `.cursorrules`, `claude.md`, `agent.md`, and context files. It does this well usually.

But now **you have to read them**. Every single line. Because you need to validate them. Make sure the AI didn't hallucinate requirements. Verify it understood your architecture. Check for mistakes.

**You're stuck reading and verifying documentation and specifications. The exact thing you wanted to avoid.**

We got tricked twice: first into writing documentation, then into reading it.

#### Project Manager for a Machine

Your job used to be straightforward: write code, solve problems, ship features.

Now your day looks like this.

Write detailed specifications for the AI. Review its output. File tickets (prompts) requesting changes. Validate the changes. Send it back for more revisions. Review again. Rinse and repeat.

You're managing an unpredictable contractor who ignores half your instructions and needs constant oversight.

**You became a project manager for AI.**

I got into infrastructure work specifically to avoid this. To avoid project managers, requirements that don't make sense, and the corporate machinery. To just write code that solves real technical problems.

Now AI has brought all of that back, whatever you are working at Fortune 100 company or have weekend hobby. Except instead of managing people, we're managing machines.

#### The Early Adopter Tax

Right now, using AI coding tools means learning prompt engineering (a skill that'll be obsolete in a year), writing detailed context and rules files (documentation by another name), keeping up with rapidly changing agentic pipelines, and evaluating tool flows that shift every week.

Most tools are just wrappers around the same base models. Feature parity is converging. You're paying an early adopter tax to do the work AI was supposed to eliminate.

#### What This Really Means

This whole situation raises uncomfortable questions about labor and meaning.

Should automation free us to do what makes us happy? Or is there inherent value in work itself?

The answer should be simple: empower people to choose how they spend their time. Let them do what fulfills them.

Right now, AI coding tools are making that choice for us. And in my opionin - choosing wrong.

#### The Bitter Irony

We automated the creative work and kept the documentation.

The one thing I loved doing (writing code) is now delegated to a machine. My "hobby" is explaining what I want in prose, then validating what came back. That journey from C to Perl to PHP to Python to Go? All those languages learned for the pure joy of solving problems? Now it's reduced to writing specifications for an AI that might ignore half of them anyway.

**We wanted AI to do the laundry so we could paint. Instead, AI is painting while we're stuck doing laundry. And managing the painter.**

And if we get bored waiting for the AI to finish? There's always TikTok in the sidebar.

I used to switch between languages because each one was fun in its own way. Each new syntax was a puzzle, each successful script a small victory. Now the victory belongs to the machine, and I'm left writing documentation about what I wanted the victory to look like.

We might never enjoy painting the same way again.

**The genie gave us exactly what we asked for.**