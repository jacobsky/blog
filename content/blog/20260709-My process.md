+++
title = "Being a Centaur with LLMs"
date = 2026-07-10
tags = ["AI", "pi", "workflows", "development"]
+++

So after using my last article to really articulate my feelings on AI and making use of it in my day to day programming, I've been trying to work to embrace "Just how can I get this to work?". As per my mental 0 through 5 model, I have not seen nearly enough to indicate that anything above a "heavily reviewed and steered" level 3 can be much good in the long term.

In summary, I think the best way to think about it is best described using the terminology of Automation Theory. A centaur in Automation theory is anyone that is augmenting themselves with some kind of technology or automation is the equivalent of a human riding a horse, aka a Centaur-like thing. In my article talking about the various levels of vibe coding, what I am mostly struggling to realize is that I can summarize it as such:

> **Be a centaur, not a reverse centaur.** i.e. Make the tool an extension of yourself and your abilities, do not become an extension of of the tool (because usually all you are is the fall person).

## The Review Problem

Like, the main problem that I am trying to address is ensuring that whatever process keeps the tools _working for me_. If I hold the liability for the tool's use, being effectively a slave to the tool is _really freaking bad_. And that means an absolute ton of code reviews. The main problem is that reviewing code is a pain and having to review a _massive_ amount of code all at once is even more painful. Like, even when reviewing junior code, I'm not going to entrust a junior with a massive PR at the start. Not going to send them off and be like "Okay, come back after you have implemented this feature", I'm going to set goals and check in with them as they work to ensure that they are on track.

Claude is really like an even less reliable junior engineer. I can't handle reviewing a 1k line PR for a feature to figure out which mistakes are which, nor are most review tools set up to catch slop (like duplicated structs), so it ends up being a scenario where I'm just tempted to just do 10 to 20 turns of AI reviews and LGTM the damn thing. Reviewing the code is a helluva lot harder than writing the code as you always have to be twice in the first place. After all the main problem _is not_ that the AI is bad at programming. Honestly? They're probably on the whole better than I am for code writing speed and solution finding. The problem mostly comes down to that it's almost too incentivized to write code and program and not incentivized enough to stick to sensible software patterns or pragmatic architectural use etc.

## The Duplication Problem

Like, any sensible engineer will look at the need for message passing and go "Oh, I guess I'll make a DTO that is compatible with my preferred serialization library" then we'll just send it via HTTP/AMQP/MQTT/whatevertransit and you're good! But claude? That clanker looks at that solution and scoffs at it. "Pfft", just convert put it all in a `Map<String, object>` and serialize it to a JsonValue type and handle it separately on each end...

Okay, so there are absolutely scenarios where that makes sense. Usually it has to do with some kind of JsonRPC-esque system where it's just not practical to provide the raw binary data types in a distributable format. BUT IT SHOULDN'T BE THE FIRST THING YOU REACH FOR.

This kind of experience isn't like rare. It's really dang common to have LLMs go off the rails creating new copies structs, datatypes and functions that already exists. While this is _technically valid_ and may even "work", it comes at a price of significantly higher maintenance burdens down the line. Where a fix you _thought_ would propagate through other fixes turns out to not at all.

And the thing is, it doesn't matter how good your prompt is or how many AI reviews you have, they still sneak through (case and point, the scenario I cited was something that has happened to me personally in the last week at work). This is not the exception, this is the rule of how these clankers are trained.

## When They Work

That being said, _when they do work_ my freaking god they fucking work. The key point of my testing lately is to see if I can come up with a process by which I can get the best of the AI code generation without the sloppy cruft that emerges.

In my prior experience there are a few key challenges:

1. **Agents can't be trusted to work independently** (regardless of model quality)
2. **Duplicating structs during implementation**, even when referencing existing structs in the plan
3. **Reviewing an absolutely assload of code** at the end of implementation is exhausting and prone to "fuck it" moments
4. **It's significantly harder to course correct** at the end than in the middle
5. **I fucking hate watching the agents work**

The 4 problems above are kind of the big thing that I dislike about working with clankers and the inherent risks of working with them. To this point, I've mostly done the usual. Trying to double down on AI reviews with `hunk diff` to help me sort through the code after the fact; trying to find and adjust the duplicate methods; trying to really nail and refine the plan. But the problems kept emerging. Still, I was kind of faster than I would be otherwise, so I just need to refine it a little more.

## The CNTR Process

Well, leave it to YouTube's "the primeagean" to give me the solution on a silver platter. No, not his existing process as a whole, but the one specific part of the process of "Stubbing out the files and putting the plan's TODO in their place". For the Primeagen, he seems to have the clanker write _less_ for me, I have them basically lay out the entire plan for each chunk of the code.

In this project, I have developed my own spin on it called **CNTR** (short for Centaur). The idea of the name is to keep the project focused on being about augmenting existing processes.

The benefit primarily comes down to one thing: I can fucking understand the plan at a glance now. `Plan.md` files or whatever are an improvement over attempting to solve everything in a single chat message of "Please implement [my project] make no mistakes". Instead what happens is that I get to see _in hunk_ where each change is going to happen. Each stub becomes a solution and architectural proposal that I can edit/delete/refine and research.

Overall I've split things into several "steps".

### Step 1: Discovery

Well this technically isn't really a step. It's more of a pre-requisite step to everything, but we're calling it step 1. Like all good engineering, doing the background research to better understand the requirements and how your development will be interacting with the system is critical. This step can be completely manual, clanker assisted, or clanker driven, it doesn't really matter as long as you can establish your own understanding of the issue so that we can build our initial design doc/spec to drive the work. (Yes, this is just a micro waterfall).

Anyways, in this phase for me, I like to end up with a basic and TODO.md and distill all my research information into a single document that can get fed in and summarized.

What needs to be done is the key in successfully navigating a problem. With and without the clanker. This is always important before hand. Knowing the background for your task is pivotal regardless of software and I think it's critical that no changes be made to this part of the process.

> **Important**: Be made for this. make no changes to this. The one thing that I _do_ ensure is that for this process I always have the clanker generate a TODO.md to hold the TODO list and a PLAN.md to hold the background details and prompts. Even with this process I have found that you are able to more reliably get decent stubs if you have a plan rather than skipping it.

### Step 2: Foundation

This creates and refines the basic data structures that will be used. The critical piece (also inherited as an idea from the primeagen) is that your data structures should only change during this Step. I tend to agree as the rationale is.. If you find out your plan was flawed, your algorithms and code often require big changes anyways, so you often might as well just trash it and start from here if you need to edit your data structures. The most important part is to really get the foundation for the rest of your iteration ironed out. Writing the glue code isn't as time-consuming if you really get your data structures right.

### Step 3: Scaffolding

Creating the stubs with comments about the plan inline for review. The critical portion of this is that unlike the typical LLM "create a planfile" the plan itself goes inline. This is the key differentiator in that rather than reading some long ass spec that says "At FileName:25-46 implement this thing", we see the comments and structure inline. We can see if

### Step 4: Implementation

Actually let the LLM atomically implement each TODO which should serve as a step by step recipe that you have refined to solve the problem. If the first three steps went well, this is usually braindead for the LLM. It doesn't have any thinking to do, it won't go off the rails, it's just going to translate the comments into code in a very fine grained short lived approach.

## The Iterative Loop

Realistically there's no need for a formal step 5, you should be reviewing at every step, but it doesn't hurt to have a formal review once everything is complete to help drive the next iteration.

Yes, these four steps are intended to be a single iteration loop for a project. This isn't a magic one shot option, but rather an engineering workflow for completing tasks. It aims to keep you _near_ the drivers seat while leveraging LLM Agents to do the heavy lifting on the code. As people have said "Coding is the easy part", but the hard part is getting to the point where you know what code to write. LLMs can drastically speed this up, but that speed can come at a cost of understanding if you aren't careful. The liability is born from treating the LLM as a compiler which isn't an accurate comparison of technologies. While workflows like that have their place in disposable projects with minimal needs, but for everything else this process has been fantastic.
