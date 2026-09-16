+++
title = "Jujutsu"
date = 2026-09-17
draft = true
tags = ["vcs", "jj", "workflows", "development"]
+++

"Source Control should not be something you have to think about" - Casey Muratori

The above quote was something that was mentioned on the Podcast called the standup and at first, I was like "But... you always have to think about it, it's just possible, is it?" and went back to sheepishly using git with whatever tools I could find to try to work around the friction that is a really rough user experience.

From lazygit, to worktrunk, to hunk, to tuicr, to fork, to kraken, to any tool that I could try to get my hands on, git has _always_ been difficult.... until Jujutsu.

## Why Jujutsu?

To be honest, I wasn't really interested in learning another source control tool. My policy around tools is to never add a tool that isn't solving a problem.

I introduced worktrees into my workflows because I found myself needing to pop back and forth between branches with increasing frequency.
I introduced worktrunk into my workflows because I found lazygit to not be enough to ergonomically create and prune worktrees.
I introduced hunk into my workflows because I was having trouble with getting the context from agent driven code reviews without the direct code context.
I introduced tuicr into my workflows because I really had trouble following along reviewing code in github rather than a terminla where I code 90% of the time.
etc

So what did I get by introducing jj?

- An undo and redo button
- Conflicts that I can deal with later.
- A code first workflow with a tool that I only have to think about in the context of saving my work.
- A tool that make check pointing and committing easy.
- The end of branch mangement
- The end of worktree managemnt
- The end of rebase hell
- I don't have to make my team change anything because it uses git under the hood

And all this took me less than an afternoon to learn (thanks to the lovely `jjui` tool which makes this transition super seamless). More than anything, the undo feature is probably the single greatest part of the tool. You can (more or less) experiment and move commits around with reckless abandon knowing that you can always reverse the op log however far back you need.

It's strange, because I'm so used to there always being tradeoffs. But somehow jujutsu manages to give me all the power I need with none of them. And it's become a core part of my workflow since day 1 of adoptin it. It's legitimately become difficult to reutnr to git based workflows.

I think the one thing that has been a bit of a challenge is fixing my brain to not think in terms of git.

## Branches

In git, branches are foundational places where you put changes. You first have to create a branch of some kind to start making changes. So if you have changes A, B, C that should be parallel changes, you have to create one branch for each, then make the changes and commit.

In jj, changes exist with the sole requirement that they have _some_ parent commit (or are root). For the most part, they just exist in their own space, and only their relation to other changes creates branches. If you need A, B, and C as paralell changes, they may share a common parent, or they may not, it's flexible. If you want to reparent, it's a trivial rebase operation (maybe you have to resolve a conflict with `jj new` and `jj resolve`) and suddenly they are a series of changes. Which one might call a branch.

This leads to how it handles the creation of branches by using bookmarks and then walking back from the last common ancestor.

## working copy `@`

The biggest difference is something that I'm still getting used to. Every change is a part of the "working copy" i.e. your currently selected commit. There is no index/staging area nor is there a concept of unstaged changes.

If you have been used to leaving a bunch of random config files with your developer specific settings, you have to be kinda careful that the files don't end up leaking into master. Though it's better for you to just make a proper .gitignore so they don't do that anyways!!!

The biggest thing is how you can pretty trivially swap between branches. It doesn't require a workaround like making a new work tree to hop to, or abusing the stash. Just move to whichever commit and things just work:tm:.

## Workspaces

Kind of an extension to the working copy. Workspaces are the equivalent to a worktree, though unlike worktrees, the only time that you ever really need separate workspaces is when you need to work on two changes concurrently. As in, literally at the same exact moment.

Scenarios you would have needed a worktree for that you do not need a workspace for:

- Switching from your current development to review some changes.
- Swapping from current work to fix a bug on a separate release.
- Pausing your current development to port a change to another bookmark/branch

Scenarios where you would still need a workspace:

- Hopping to another branch to review something while your application is running regression tests
- Running an agent on a background task while you work
- Running multiple agents in parallel on different tasks.

This leads to like, the final form of the worktree here you really don't have to prune them ever., You can just let the forest

## Final Word

It's incredibly rare to find a tool that solves problems while introducing _none_. I cannot recommend jujutsu enough. It is both perfect for veterant and junior evelopers alike and you owe it to yoursef to give it a shot. It's free~

, I'd highly recommend you give it a whirl here: <https://github.com/jj-vcs/jj>
