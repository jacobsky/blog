+++
title = "Cool tricks with Jujutsu"
date = 2026-10-06
tags = ["vcs", "jj", "workflows", "development"]
+++

Jujutsu has really been probably the most transformative tool for me in the past year. It's probably on the same level of growth as when I finally fought through the barriers and learned vim motion. With that, I keep finding new thing that make my jujutsu experience even better.

## Cool thing #1 Aliases

Managing git has always been some what of a "build your own muscle memory" kind of thing, with many arcane workflows resolved by wrapping other tools, using one of the super powered guis or tuis, or just generally learning to deal with it. More than anything, dealing with history tyupically meant that you neede to remember specific syntax for things, but you can quickly and easily configure various aliases for kinds of commits you want to look at.

For example:

```toml
[revset-alias]
wip = "description(glob:'WIP*')"
```

from there you can use it in other locations to trivially check for wip commits using something like `jj log -r wip`

## Cool thing #2 Jujutsu private commits

With the above aliases something that you can also do is use them to help define private commits. In JJ, a private commit is any commit that it will refuse to push.

This sounds relatively useless unless you, like me, like to keep scratch files located in your current working directory before deleting them when you no longer need them.

I like these for project settings, random ideas for whatever I'm implementing, along with rough draft notes that should be transformed into proper specifications or decision records before things go public. (Yes, I may be a little too used to git's staging area as my personal temporary dumping ground.)

I have mine configured similar to the following:

```toml
[revset-alias]
wip="description(glob:'WIP*')"
has_scratch="::files(glob:'.scratch/**')"
private = "(wip | has_scratch) & mutable()"

[git]
private-commits = "private"
```

With the above, any attempts to `jj git push` with scratch or a wip description will fail, and this includes any ancestor commits that are linked to the current bookmark. Really handy to prevent accidental pushes of things like incomplete docs, random logfiles, etc.

## Cool thing #3 Jujutsu as a WSL/Windows bridge

For my job, I have to run my code changes and test them in both a docker environment as well as a more traditional windows deployment environment. Before testing in windows was rather tedious having to push a remote branch and copy past between the two, but it's actually rather trivial to just point jujutsu at a local directory path as origin.

Using the revset alias, it's made even easier as you can define a pattern like `bookmark(glob:'win-*')` which I can use in tandem with jj's git export feature to help pull only the branches that I want to test in windows over and trivially get to testing

## Cool thing about jujutsu #3 Easy Stacked PRs

A number of git providers/extensions (including github) have started allowing Pull Request Stacks where you can associate multiple pull requests with a single commit. In git this tends to require multiple branches, but you can get this for free by just continuing to develop on your main PR and just adding new bookmarks and the stacked PRs will naturally emerge. Making a change to an earlier PR is automatically reflected when you next push, so there's no need for any tedious rebasing (it just happens as you add new commits and stack things on top of eachother)

Honestly, I've been amazed because I don't really need to reach for anything other than jj for 99% of my day to day version control operations. It's just that good!
