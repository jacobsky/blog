+++
title = "A simpler observability stack"
date = 2026-05-16
tags = ["AI", "workflows", "development"]
+++

After spending the last 6ish months with Grafana, I'm finally parting ways. It's not Grafana, it really is just me. As most of my side projects are here for hobbies, batteries included options are really just much much more important to me in the longrun.

## The good times

Grafana was really the first time that I saw myself actually using non-first party observability tools. Prior it had all been things like Azure monitor and Kusto Query Language based observability (which always kinda _sucked_, tbh) so getting to experience the joy of self hosting an observability stack that kicks the pants off microslop was a lot of fun.

A short list of things that I loved:

- Tight integration with tools
- Community shared dashboards
- Logging and traces that make it fun to investigate issues
- Integrations with every kind of reporting service under the sun

## Why I'm Leaving

If I had to criticize it for my workflows, it mostly just comes down to the fact that I'm kinda lazy and have limited freetime. There's a different kind of enjoyment in building dashboards _when I'm being paid to_ than at 10pm at night when I've already spent ages working on stuff during the day.

The fun part of my hobby is building stuff, not necessarily tweaking my observability stack. In services hosted on a single server via docker, I just don't have the need to get fancy distributed traces to identify bottlenecks in my code. It's a single program with a database. It's just not that complicated! YAGNI rules apply (That's "you ain't gonna need it" for those not in the know).

## The New Stack: Dozzle, Beszel and Prometheus

In we bring the two newcomers. **Beszel** is a simple Physical node monitor. Though, I technically could probably skip it as I'm on dokploy which technically does collect node metrics, but beszel is lightweight, trivial to configure, and can be connected to any number of services via webhooks so I can get notified if the server is getting hounded by DDoS.

**Dozzle** on the otherhand, is a very lean, but powerful log aggregation and viewer. It even comes with it's own SQL based query language to help narrow in on any potential issues. Perfect for individual apps that don't have a whole lot of distributed tracing involved. It's enough for me at the moment.

The one part of the grafana stack that I kept was **Prometheus**. There's honestly not a better metrics collection service that I have been able to find for open telemetry related stats and there's plenty of ways to craft lightweight websites and templates for displaying all your metrics. It's also got it's own versions of alerting which makes it a very handy tool for monitoring individual aspects of your services. Though I still haven't quite figured out how to get it to automagically discover individual services, so I have to periodically update it's config and reboot it. Not the end of the world, just a wee bit annoying.

## Uptime Kuma

Though, when writing this I almost forgot that the last thing is **Uptime Kuma**. This is another tool that works similarly to grafana's own baked in alerts, _it's just so much easier to set up_. One of the problems that I faced with grafana was getting a simple alert message to be posted in discord. I have a small number of users and just wanted to be able to have it post via webhook when the site is experiencing notable problems. With grafana, it basically gives an incidence dump (again, exactly what I want with my team, but _not_ great for personal projects where I am a team of one).

## Conclusion

All in all, I'm pretty satisfied with how the stack has turned out.
