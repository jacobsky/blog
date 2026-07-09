+++
title = "The Scale of LLMs in Engineering"
date = 2026-07-01
tags = ["AI", "pi", "workflows", "development"]
+++

Like any technology, the main differentiator between a "good" technology and a "bad" technology is in the usage of the tool. LLMs have exploded in popularity in the past year gaining incredible traction as coding agents. Far beyond the "Chat window" and "LLM code autocomplete" of the ancient days of 2024. What I'm finding is that we're still very much in the "hype craze" phase of figuring out where LLMs as tools really fix in. What makes a good AI agent project? What makes a good coding agent? What makes a good general use agent?

There's two answers, the honest answer of "Uhhhh, tbh, no fucking clue" and the lie "writing code as a software engineer is basically over".

The reality is that there's a lot of nuance in using LLMs for software engineering. Probably the first of which is a few key facts.

Fact 1. LLMs are very complex auto complete engines.
Fact 2. LLMs are very good at writing large quantities of code.
Fact 3. The quality of that code can vary heavily.
Fact 4. As a result of fact 1,LLMs do not have any intelligence or understanding of the code they produce. They do not reason, they just write code continuously until their loop arbitrarily stop.

What's important is that -- contrary to some more stubborn beliefs -- lack of proper reasoning capabilities does not mean that LLMs are inherently useless. Instead it means that they  are simply another tool in a software developers arsenal to build complex systems and software. The problem arises that they are a tool that needs to be used with a lot more caution than prior tools. Note Fact 2 and Fact 3 are the main challenges that you face when introducing it into a systme. As has been identified before, the biggest problem is the quanity of code that can be written by tools massively dwarfs the amount that can be easily audited.

I think it would be helpful to get everyone on the same page, so let's outline the various levels of AI use and discuss whether they have a place in modern Software Engineering workflows.

## Level 0 - No AI

This is pretty self explanatory, a no AI workflow is just continuing to work like it's 2020. It's fully dependent on your older tooling. Everything is on the engineer and only the engineer. It's all you all the time.

The major key benefits of this model is that engineers adopting this method will never risk deskilling, will never risk losing track of what is happening in the codebase, and will likely be able to deliver more consistently (though engineering skill issues apply).

The primary disadvantage is that humans simply cannot write code as fast as LLMs can. This means that feature delivery takes more time and human energy to implement. In any environment where feature delivery equates to funding (such as in an early VC backed venture phase), this may not be entirely tenable (investor KPIs typically don't include pesky things like uptime, performance or stability into the picture).

## Level 1 - AI Auto Complete (e.g. Github Copilot Tab autocomplete)

With this scenario, it's still the same old No AI workflows. Engineers are still working out of their editors/IDEs where the only significant difference is that you have a handy hotkey that completes your work. Often this can come from a command that will accept or cycle an autocomplete.

Effectively, this means that you have a workflow that is basically identical to Level 0, just slightly faster. Most of the workflows don't change significantly. Engineering is still 100% human tasks. It has the same key benefits as Level 0, with a minor benefit to speed. AI autocomplete is not going to cause your code output to explode, but many of the more tedious small lines of code get updated quickly.

Note there is still a small chance of deskilling, but because you are building a mental model of your project at all times, you really don't end up losing much for the tradeoffs.

## Level 2 - In Editor Agent/Chat

This is the first time that we break into more "modern" AI workflows. And naturally, this is where the LLM runs more autonomously than prior. Engineer still stay firmly embedded in the code typically writing things by hand. Tasks will involve still writing a lot of psuedo code, function definitions, and much of the core operational logic and domain models, but AI may be used to accelerate certain areas of development. Strategic implementation can help to fix pain points with documentation, code reviews, writing boilerplate, spot checking bugs, interpretting logs, etc.

Risks of deskilling here, can vary in severity depending on the amount of agentic work executed. However, for the most part any deskilling is the same as the deskilling that has occurred with thge advent of LSPs. Processes (when implemnented well) simplify development without incurring cognitive debt and strictly allow you to be more productive.

## Level 3 - External Agent prompting

Level 3 is where teams will either find a natural stopping point, or reach point of no return into what actually can constitute "vibe coding", where teams can find that they get lazy and just try to let Clanker of Choice:tm: do everything. This is where it's possible to get into pure vibe coded slop territory. pure vibe coding territory. The key deciding factor is mostly the "what" you are choosing for external automation.

For tasks like helping with code reviewing, linting, and other general non-programming tasks. Sometimes it can make sense to offload a task to an agent to triage (especially in the mundane and otherwise boring task) and these kinds of issues can be fine to just leave to the AI. Sometimes they will one shot it, and it saves you time, sometimes they won't, but you'll have a starting place.

The risk is that sometimes people will get overly ambitious with what an AI can do, and give tasks and the ability to learn and guide the agent from within.

Probably the biggest indicator of when Level 3 becomes highyly risky of producing slop is when the `git` (or other VCS) is typically when the sloppification happens. The reality is that turning over direct VCS control creates the first point where -- even engineers like myself that swear the AI is doing fine due to tight sourcecode review,  by reviewing every bit of source code, the most is the first point the actual code itself becomes more of a gambling machine with people creating fancy markdown documents to give the illusion of control surfaces for improving quality when the actual core problem is that agents just like generating text a little too much (and don't have intelligence).

## Level 4 - Agent Swarms

This is the point that I find most projects to veer strictly into unmaintanable messes. Yes, meaningful work can get done with agent swarms, but it comes at a price of maintainability risk of slop. The main problems with agent swarms tend to be the nature of them being distributed systems of randomness. Agent swarms _may_ have a use, but the problem is largely the unpredictability of how the internals come out.

At this point, I have no doubt it's possible to make decent use of them in certain design scenarios, but it still leads to an auditability problem where subtle bugs are just... a massive pain in the ass to audit and you sacrifice most of your technical understanding of the system for a blackbox gamba machine.

## Level 5 - Delusion of Human intelligence

This is the point where we follow the idea of infinite agentic loops, independent coworking assistant agents, and mostly just token burning monstrosities come into play.

It's not that level 5 is some kind of hellscape, it's really just level 4, with more agents and even less humnan interaction. The trust in this case completely flips into long term gambling of tokens until you get some semblance of a functional blackbox test. But -- there are many downsides to this approach. First and foremost being cost (more than just tokens). The biggest problem being one of accountability. We know that agents hallucinate, so when they hallucinate a vulnerability what do? Well, we as engineers have to be accountable for an ungodly amoung of code review.

## The sweet spot

The thing is, for as critical of AI agents and LLMs that I am, I don't actually think it's reasonable to ban AI for use in programming. Nor do I think that it's a terrible tool. It, like Springboot, is just a tool that can be misused in areas that it shouldn't. There are entire classes of problems that AI just makes so much less tedious. If you can afford the token spend (i.e. using cheap LLMs) they can absolutely be a fantastic way to claw back extra time that you have to spend to do things like "wiring up boring json RPC apis"; triaging and issue; searching for which module did that one thing; or "adding more logging to APIs". As engineers our most important skill is our ability to analyze and think about the important problems. Being able push those tasks to an LLM is a fantastic way to maximize your ability to think about the parts of your application and services that really matter. As such, I think that it's worth engaging with AI somewhere between levels 2 and 3, depending on your level of discipline.

The biggest key point of discipline is ensuring that LLM contributions are vetted and well understood. This requires a lot of work depending upon which moel yo uare using. There are a few guidelines that may help you build up a better process for engineering with LLMs.

1. LLMs are not allowed to use git. While some kind of hook may be useful for a system like jujutsu (which keeps a rolling log of every change and command in repo), the key point is that commiting is a human responsibility. Giving this over to the LLM means a high likelyhood of just leaving it to the LLM instead of taking responsibility for your tooling.
2. Use the cheaper models. The reality is that while more powerful models like Claude Opus or Open AI Codex are definitely better at performing the long running tasks, the limits are out of plain cite. Rather than getting obvious bugs, you get subtle bugs, When you have a cheap model that completes your task by stubbing a function and writing "TODO: Finish this function", that is a massive call out to the limitation that you are working with.
3. Stay in the code. Even if you don't write it, you need to read everything always.

I'll work on another blog post later detailing how I'm creating a process that grants me better control over the LLM output and has lead to very successful feature implementations without sacrificing quality in the next wone..
