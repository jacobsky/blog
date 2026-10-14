+++
title = "Your stack is Economics"
date = 2026-10-10
tags = ["stack", "software",  "development"]
+++

Something that has been kinda interesting lately is the sheer quantity of Rust projects that I see popping up and it got me -- as someone that liked Rust before it was cool (well, it's still not cool, but it's getting used outside of the rustaceans)-- got me thinking some. Partially a little sad since it means that any given project has a relatively good chance of being vibe coded, _but also_ because I realized that a lot of the choices of stack are heavily influenced by economics.

## But why ~~male models~~ Rust?

Just to catch up, why is Rust popular for vibe coding anyways? It's mostly because it's a really solid language choice for a lot of classes of problems for Hand Written Programmers which inspired a passionate community to experiment rapidly and build many tools around the rust ecosystem that are pretty solid.

### Reason #1 The Compiler

The Rust compiler is probably the main reason for this. While it gets rightfully criticized for slow build times, the trade-off comes in the form of the borrow checker which serves to validate mutable and immutable state (compile time memory and thread safety), algebraic enums that allow you to make invalid state unrepresentable at compile time, and some of the most informative compiler error messages (it is not uncommon for the error to tell you exactly how to fix it).

### Reason #2 The Crates

The Rust community is one of the development communities actually deserve more credit than the compiler designers for making Rust just really really solid. There are many prolific crates that heavily accellerate development like `serde` (serialization), `tokio` (async runtime), `sqlx` database stuff, `crossbeam` (high performance queue runtime), `rayon` (trivial parallelism), etc that are fantastic ways to bootstrap a project with high quality components.

The last few years we have seen a gradual addition of proper full stack frameworks starting to appear that you really could start to use in a production level business.

### Reason #3 Performance

Since the compiler can do a lot, it's pretty common for safe rust (offloading most of the critical unsafe handling to community libraries) with proper clippy lints enabled, and you can get a pretty high performance application with minimal fuss. You already get the

### Drawbacks that slowed adoption

## Wait, so what was this about economics?

Right, I got side tracked talking about Rust. And part of it is because it's kinda relevant. For a few years, Rust was one of the languages that won the "Most liked language" from one of the development surveys and it's always had a pretty passionate community of users. (So passionate that it causes people to try to build libraries that seem insanely out of place like `Dioxys` a React full stack equivalent), but there's a few major reasons that it didn't exactly pick up. While there were developer experience issues that definitely showed up (e.g. sometimes awkward syntax, lifetimes, proc macros, build times), I have never really believed that they were the main culprits to the lack of wide spread adoption.

The real reason for language/stack popularity and proliferation is far more closely linked with the economics at play. Rust, for many companies, is simply not economically viable to meet the business needs.

But Rust is fast, with a Robust Compiler! It is, but there's a fair bit of learning involved.
The crates are great! But many of them lack the industry support of something like `Spring` so you are likely to need to invest _some_ time in bug fixing and supporting.
But it's fast! And Most importantly! CORRECT!!! No Undefined behavior!!! Also _yes_, but it's kind of a trade off.

I've talked about spring before and the fungibility of developers being important. Something that I also neglected to mention is the practical application of "there's a starter for it" i.e. You have a problem, odds are _the industry did_ and due to Java's popularity they made a well vetted and maintained package that you can use. Rust, on the otherhand, doesn't always have that. This means that choosing Rust puts you in the position of choosing that edge case for your stack.

Similarly, depending on what you work on, the viability will be different. There's a reason that cloudflare (backbone support for internet stuff which is basically infrastrucute stuff) heavily prioritizes Rust in their stack, the reliability and performance is a _business requirement_. The needs of a fledgling web startup, tend to be more oriented around feature delivery and experimentation. Correctness and performance may not matter when the actual value of a feature or application is uncertain.

Furthermore this extends far beyond just application toolchains, but also the actual underlying stack. Postgres, MySql, and Sql server tend to be very popular as you can often find some kind of managed service just about anywhere. While Turso (still great) is probably less likely to be pulled into a stack due to it's relative newness. It's still pretty risky and not necessarily economically viable to choose depending upon the risks.

It's important to note, that economic viability doesn't mean a technology is good or bad, it just means that it lacks enough support to base your business on.

## Side Tracked: On Cleanroom Reimplementations

In the news recently has been an uptick in the idea of AI reimplementations of existing softare, the two big ones being "reverse engineering and combining games" and Adobe's whole Creative Cloud Suite being turned into open source reimplementations that sort of work. While the media frenzy seems to be painting this as "the end of software" and "what if AI breaks everything ever?" the Artcraft, or whatever the thing is called, isn't the disruption to Adobe's business that it is being framed as.

Certainly, "Photoshop, _but free and opensource_" is pretty compelling for a casual users, but that's nothing new. Krita and Gimp have been (mostly) viable alternative for it for ages, just like Libre Office has been a (mostly) viable alternative to Microsoft Office/Google Docs. But even with that, the vaste majority of businesses continue to purchase the proprietary licenses and use those products instead of FOSS because the _support matters_. This doesn't preclude someone else spinning up a competitor based on providing support for FOSS implementations (or Libre Office), but -- while I'm sure we will see that start taking hold in the EU over national security concerns -- it takes time for people to build up the necessary cred.

## Back on Topic

The support is actually the a major sticking point to usability of most software projects. And it affects different parts of your stack differently. For programming language support often means resources, responsiveness to CVEs, getting new updates, maybe integrations.

Infra Stuff, having an SLA that helps let your sales folks sleep soundly at night knowing that most problems will be something you can blame on someone else upstream.

For IT departments it often means "How long do I have to wait for the support line to pick up when there's a problem I can't solve?" and

For InfoSec, it's "how long until the next big CVE is fixed?"

## AI Agents Change the Economics (For better and Worse)

**Disclaimer**: I am an AI Skeptic and of the ususal "Fuck Around -> Find Out -> Software Developmnent Actually Changes" I hold the opinion that we are very _very_ clearly between the first two phases. Nobody knows how the long term economics will be shaped over the next five years.

That being said, one thing that is clear is that AI Coding Agents can provide heavy economic incentives to push your stack choice in any given direction. Naturally, agents can write code really freaking fast and can easily create 10x the code output. Even if you are stuck in meetings all day you can still have your clanker churning away and it'll develop for you! (For legal reasons, I'm being sarcastic). Regardless, even outside of _actually writing the code_ (which is faster) even just being able to offload certain tedious tasks around coding like QA (agents with a cli can be pretty good for adhoc testing), writing test code, and even things like updating documentation, PR descriptions or code reviews can be a serious accelerator for software development. (Personal opinion: I hate writing code with AI, but I love using AI for annoying non-coding tasks).

Naturally, software engineering as a whole still heavily utilizes LLM workflows for writing code and creating features, bugfixes, etc. While certain aspects of the economics (supportability, SLA, documentation, quantity of examples, etc) still remain, other aspects start to change. Aspects such as verbosity and developer experience may be deprioritized over something like strictness. There is a reason why Typescript became more steadily prioritized over Javascript as many developers embraced clanker workflows. Typescript always gave good feedback, but the extra friction of a build step was a cost that not all teams wanted. Well, for clankers the trade-off makes more sense! Typescript feedback means clankers have more criteria to guide their development, that in turn means fewer runtime errors, which means less wasted time (also wasted interventions with a programmer). This extends to a language like Rust where setting a couple of defaults for your clanker's builds (safe only, treat warnings as errors, enable relatively strict clippy lints) and it can give you the confidence that the code that gets built generally works. (Caveat: It's still got the clanker unreliability, but the success rates rise)

Furthermore, test code becomes significantly less tedious when you can outline your test cases in English after you have verified things are working, have the clanker write regression tests to ensure that later changes don't break it (or you are at least aware of the breakage). Testing and documentation were many programmers bane because of _how fucking boring_ it is an incredible time saver. And no one is going to miss writing tedious test code or doc comments for things. (Note: This does not decrease the number of technical writers you need, they are invaluable for proper educational content. The clanker docs are just for those things that may or may not change next week). Let's be honest now, docs and test code are almost always _slop_ whether written by clankers or not. (Note: There is still some level of judgement for the quantity and type of each test. A flakey test is a worthless test, after all).

When you can combine the accelerated test code writing with allowing clankers to do simple triage and bug fixes, you can really free up the time to actually work on thinking about more impactful problems. You don't need to be running opus to fix many low hanging fruit bugs (e.g. Off by one error, it's `x >= y` everywhere else in the codebase, but `x>y` in this one specific spot). That bug hunt may not take haiku 5 minutes to produce a PR based on an error log which might have taken a human 30 minutes to pull everything together between the triage, fix, commit, fixing CI lint errors that apparently failed, writing a detailed explanation in the PR that will satisfy the grouchy maintainer that needs PRs to meet a minimum word count, etc.

At least for me, I'm not losing sleep over dumb bugs being offloaded to AI. It really matches what they are good at "pattern matching" while they are basically completely abyssmal ass at architecture.

Back on topic, we're also in an interesting time where performance is starting to make a comeback. When writing code isn't the bottleneck, you can pick stacks that prioritize speed at the cost of developer experience (because let's be real, you don't have to write it all anyways). This more relevant in organizations with more mature deployments where their cloud spend covers a more sizeable portion of their budget. _Especially relevant_ as the SaaS model has started to feel the squeeze in the last couple of years. When the schedule and talent pipeline isn't significantly impacted by your choice of technology, you can start to optimize for other factors (such as performance, which means lower cloud spend).

If your schedule isn't significantly impacted, being able to drop your consumption by picking a faster, more performant stack. If your app is double the speed, you can effectively halve your fleet size (or VM size). Like, realistically, the engineering time is still the most expensive part _for most organizations_. Most apps can comfortably be served top to bottom on a single 128 vCPU/256GB AWS EC2 instance (about $4k USD per month at time of writing). And you have to weigh any cost reductions against the engineering time to handle it. That engineering time is engineering time preventing new features from being implemented, etc. _On the flip side_, if your delivery speed isn't especially impacted, lower costs means lower costs.

## But why ~~male models~~ Rust?

Are you serious? I just told you... /s

Ancient Zoolander Memes that expose my age aside (fuck, how is is it that it's almost 30 years old ;w;), this all kind of leads back to why we start to see Rust. The economics are starting to align better to favor it as a core part of the stack.

With just some basic settings (unsafe not allowed lint, warnings as errors, clippy lints, hook up SQLX to your development DB to validate SQL, etc), it's possible for teams*  to maintain similar development speeds, while increasing the success chance of their clankers, and pick up the nearly free performance gains (memory being the most important usually) of using Rust over something like Java or Python or Javascript. All it takes for Rust to become the defacto langauge of the year is to make it so that you don't have to write as much.

*: Assuming members have decent system design and are capable of logical thinking.

I'm actually kinda serious though. One of the bigger pain points for Rust has historically been that (while `.clone()` or `to_owned()` works in most cases), you may end up in a position where you cannot avoid introducing a lifetime into a function/trait for performance reasons. It comes with a catch though, you have to update a dozen method calls and files to properly ensure that the lifetime is valid across all invocations. And suddenly you get this super tedious tasks that provides no fun value. Well, in a way, clankers have solved that. Introduce the lifetime and the compiler has always guided you to write the code to support it. As such, you can just run a clanker until it compiles (just make sure that they don't get any funny ideas about removing the lifetime you introduced.

It's going to be really interesting to see how long the economics hold, but more people embracing Rust makes me happy because I'm personally too stupid to use languages like Python, Java, and Javascript. I need the compiler to guide me.

## Final Note on Economics

In terms of agents and economics, while many of the points of economics for your stack have been consistent throughout the years, we still have no freaking clue about whether or not AI will be sustainable into the future. So don't go rewriting your stack into Rust, yet. _Especially_ don't follow DHH's keynote advice for migrating everything to Rust without reading it, because it literally enslaves you to pray to the agents when something goes wrong in the future (and it certainly will). I am eager to see how the agent lead rewrite initiatives do work out.

Too many unknowns are in our future. The best thing we can continue to do is write good, reliable code in whatever stack we can understand, knowing the kinds of trade-offs. And if that ends up being Rust because (like me) you just like the language a lot, then we get the double benefit!
