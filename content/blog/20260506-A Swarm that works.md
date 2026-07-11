+++
title = "A swarm that works"
date = 2026-05-10
tags = ["dokploy", "selfhost", "vps", "infra"]
+++

In my last update, I mentioned that kubernetes had been a bit of a challenge and that I ended up with dokploy. For my own memory, I figure that it would be a good idea to outline the whole series of events.

## The beginning

One of my biggest focuses as a developer prone to tangents is that the workflow must be protected at all costs. Building, testing, deployment, etc should all be "One button/command". Every additional step that I have to do manually is a reason for me to avoid the work. This actually plagues my development process top to bottom, which is ultimately why I ended up picking neovim for the basis of editing. It wasn't because I am somehow addicted to the terminal, rather it's because of the simplicity of navigating via telescope.

Anyways, onto the experiment.

## The issue

Docker stack deployments were getting a bit finicky, especially as I wanted to update things. I always had to launch a build via CI and then -- as my swarm config was mostly manually -- I would have to login, adjust my `docker-stack` files and then do another push. This lead to me having a few articles queued up that never see the light of day as I just was too lazy to deploy things.

As I was also looking to learn kubernetes, it was a good chance to kill two birds with one stone.

## K3s and Flux

Being that I'm not super rich, I run on a relatively modest VPS which kind of limits the options to lightweight k8s options. In this case, I chose k3s. As I also wanted to experiment more with `git ops` (i.e. infrastructure managed via git repository), I chose the super lightweight option "flux". This showed to be a _bit_ of a mistake.

While it went smooth enough on day 1 and 2, one of the biggest challenges that came up was "ghost pods" where a pod gets into a bad state and kinda vanishes from your radar _but blocks flux from reconciling new changes_ (which was absolutely a skill issue on my part).

This wouldn't have been nearly as bad if it only happened on day 1, but it also happened when I was deploying my Grafana/Prometheus/Loki/Alloy/etc stack for observability. Somehow, I got a setting wrong in loki and upon correcting it, the previous pod in a bad state did not allow the reconciliation to complete and once I found the issue, it ended up dropping my entire stack in one go.

Now. This is _obviously_ a skill issue on my part, but when combined with the time wasted, I was finding that k3s is just not quite hitting the "simplicity" that I am looking for. I have limited free time and far too many projects that I want to work on, so getting things up and running in a simple and stable fashion is the priority.

I think what I realize in hindsight is that -- for me -- the reconciliation loop has to be 100% in sync with the git repository. All of my issues in K3s this time were issues where what I put in my repo did not actually make it onto my k3s node. Perhaps in the future I will give this another shot, perhaps when I get my home lab set up properly.

## Back to Swarm with Dokploy

Pouting a bit as I begrudgingly returned to docker swarm mode, I realized that the biggest points of contention with swarm mode mostly boil down to three things:

- It's done
- It's kind of bare bones
- Features like helm don't exist, so you have to build it yourself

These issues aren't insurmountable, but they require some kind of a frontend to manage it. While I'd love to build one up myself, I'm a little too busy for that, so I shopped around for open source options and ended up on dokploy.

Funnily enough, dokploy was not my first choice. It was actually the third one that I tried after both the login screens for komodo and coolify failed to load for some unknown reason (probably my fault, but first impressions matter).

Even as my third choice, it's a very very cozy option. It provides just about all that you need to make docker swarm actually... work well! It's kind of reinvigorated my desire to work on fiddling with new tech again and has greatly simplified workflows.

## The Killer Feature

If I had to identify any one killer feature it's actually the webhooks. It makes it trivial to kick off a github CI and then have dokploy pull the new image in response to a webhook. That combined with it's application templates and the ability to trivially run any docker compose file makes brings back a lot of the fun in deploying my applications.

The only downside is the lack of proper gitops, but they have a tracking issue, so I'm optimistic that we might see some kind of gitops workflow in the not too distant future.
