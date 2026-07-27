+++
title = "Human As the Loop Process Review"
date = 2026-07-26
draft = true
tags = ["AI", "pi", "workflows", "development"]
+++

After my last article, I've been putting my process into practice and feel like it's a good time to

## Did it solve my problems?

Just to review, my main problems with LLM workflows were can be summarized as:

- Code Reviews suck (doubly so when it's clanker based)
- No matter how thoroughly I review the plan, it's hard to know what code is going to change.
- Clanker code is unreliable (see code reviews suck)
- Clankers need to be reigned in or they will create unnecessary interfaces, duplicate existing code ad nauseum (even within the same file), and trend toward the big ball of muddy spaghetti design pattern.
- No model is smart enough to reliably work without consistent monitoring
- I believe that version control and committing code should be _strictly_ a human responsibility. (As the human is liable)

Of these issues, if I'm honest... yeah. It pretty handily solved every single one and that I had in addition to helping me with some of the more mundane and boring tasks that I was responsible for in the interrim.

Rather than toy problems like "Build me a calculator app", or whatever "trust me bro" benchmark I only care about legitimate use of AI, i.e. developing and deploying code for real code bases. Fortunately, as we are trying to experiment with it at work, I have a /really/ good excuse to try to work with it.

So here were some actual dev work that I did with it an dhow it ended up working.

## Real Dev Cases

### Real Use Case #1 - Splitting Up Legacy Monolith into Multiple Services

In our monolith spring boot application, we are working on trying to split up certain "heavy processing" services into a separate backend service. The primary goal being that many of the processing tools are things that require a lot of baseline resources, but don't run nearly as consistently for users. Typically only occuring once for analysis and specifically when the users make a request to do asset conversion. As such, the goal is pretty simple, move code from the `web-app` module into our `commons` and `processing` modules. This is actually pretty complex and requires a lot of nuanced changes to get to work correctly as it involves a combination of "copy and pasting the processing code" as well as creating and mantaining a new messaging boundary with all the associated infrastructure management and DTOs while maintaining the same exact API.

My first attempt at this was made without any process, mostly just taking it step by step. And it... caused me to discover a lot of issues with the implementation later in the development. It wasn't exactly ideal as it ran into almost every single issue in my list. The biggest challenge though, being the LLM _really_ liked doing the "technically correct, but completely impractical" like using a `Map<String, object>` as the DTO. Will it work? Yes. Is it a good idea? No. This was really the main driving force in the improvement plan. It's just not very fun to be cleaning up obviously poorly conceived code.

On the second module conversion, introducing this process made it surprisingly simple. I actually was able to get much closer to doing it right the first time with this approach. It's not that the LLM suddenly became better, but that I was able to correct it at the architectural level asking things like "Is there another similar function?" or "Use `ExistingDtoThatIMade` don't make `CopyOfExistingDtoThatIMade` for serialization"

Overall it was a big improvement.

### Real Use Case #2 - Migrating Windows to Multiplatform Directory Handling

This was a bit trickier, due to.... historical reasons there is a bit of functionality that ended up relying heavily on the windows Path operations. While this worked for a while, the incompatibility between it and Linux is something that needed to be addressed so that we can work on completing our Migration to a multi-tenant docker based solution.

The important facts of this matter is that there are many data asset retrieval operations that relied on the windows path structures, without actually accessing the filesystem. As such, there's just /a lot/ of places where the code needs to change in a way that is tedious to evaluate fully.

The solution ended up being to change the interface, create a regular expression to replace all the random callsites that will change, then use the LLM to map out all the remaining compiler errors and determine whether the function requesting the path looks like a FileSystem call or a hierarchy call. And make the corrections. This is one of those times where it kind of just one shot it and I was really impressed.

It honestly, actually worked. I was kind of shocked because this isn't the first time I tried to tackle this problem and mostly had to just sit in sifting through intellij for 400+ compiler errors.

## The good

The process was a clear improvement at every level. Each turn was more productive as various aspects like creating guardails in deciding on the datastructures; _then_ deciding on the actual implementation; _then_ finally doing it made the LLM hit it's mark more frequently. But the bigger benefit was that it is a lot easier to visualize proposed changes by having the LLM comment inline. With `hunk` diff tool, it's trivial to watch things build up and quickly pop into the terminal to course correct.

To be honest, the biggest issue that I'm solving here is the gap that is created between the PLAN.md and the actual code that is produced by the LLM. In particular, it's actually really difficult to know what the LLM is thinking of doing, and what it's actually going to end up going. It eliminates a lot of the pressure of code reviews to catch hallucinations as I feel like I am mostly working inside of the code directly. _Not_ trying to validate slop after the fact.

I also found that one of the smaller benefits was the accuracy and token usage. I saw token usage decrease a bunch due to how Claude would spawn subagents to handle the TODOs in order. Which meant that each CLANKER-TODO in the code became it's own microprompt from start to finish. After all, once you figure out the general shape and structure of the code, most of the code is rather self evident and just falls into place. Getting there is usually the hard part and takes a lot of turns.

## Rough Edges

Git is just not really enough for a good process. Lot of time is spent squashing and just generally trying to manage the working tree and staging. In many ways, this is solved partially by using jujutsu (jj-vcs) which I would personally describe as "git, but better" and allows for much more flexible and easy merging.

Future work: Creating a smart checkpointing plugin to help allow for intermediate state. Will probably try to aim this at allowing either jj or git as an option. The idea is to set a few exit criteria for each turn and create a new commit with certain file types.

Another thing that I found is that while it is useful to keep the LLM from altering datatypes. The process can confuse it a bit if there's actually no changes to datatypes. There are a large number of tasks that really wouldn't touch the datatypes anyways, usually any algorithmic changes (commonly bugfixes). Having entire phases that are dead weight is really just a waste of tokens.

As a result, I also created a two step process focused on bugfixes and root cause analysis. And updated the skills to allow skipping straight from context to scaffolding. Rather than requiring an explicit goal first.

Another rough edge is using skills only. There has still been a fair bit of inconsistentcy with how the models evaluate whether they are actually finished or not. (I get a lot of models like "We're done" only to `rg CLANKER-TODO` and see a bunch left). So I think that mandating this into some kind of plugin to help give more deterministic structure would probably alleviate the issues, but that does require additional development.
