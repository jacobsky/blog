+++
title = "Kubernetes is frightening, but worth it in some cases"
date = 2026-05-05
tags = ["intro", "new"]
+++

The last week or so I've spent some time working out how I would convert my current `docker swarm` based configuration to something that might be a bit more modular and professionally relevant.

## The Experiment

Armed with handy gemini subscription to help me get an idea of what the heck `kubectl describe helmrelease -n mynamespace` possibly means, I attempted to configure k3s with flux for a first attempt at a gitops pipeline. Unfortunately there was a major flaw in the synchronization for how flux works, so I ended up giving up and moving to dokploy.

## Dokploy

So... hello from dokploy! I have a lot of thoughts, and will do that in a later post.
