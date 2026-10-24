+++
title = "x-Driven Development"
date = 2026-10-25
tags = ["project development", "software",  "development"]
draft = true
+++

After reading one of the latest anthropic blogs about how they conduct code reviews and basically reading it as an advertisement for why "reviewing code like a frontier AI lab that ships slop" it got me really thinking about just... project management in general.

Behavior-driven Development, Domain-driven development, Data-driven Development, Test Driven Development, Feature-driven, Spec-Driven, Outcome-Driven.... I'm tired boss.

_Every_. **Single**. **ONE** is flawed by the nature of trying to orient development around the wrong thing.

## What's wrong with X-Driven Development?

My hot take is that it's the idea of letting something without agency drive your development.

There is nothing wrong with considering a design, test, data, feature, etc for your project management, but making it the "driving force" in your project management style typically means that you reshape your process to fit the model you have chosen at the expense of the potential agency within your team and organization.

In practice, most of these end up orienting themselves around tools, KPIs and other flawed information that pushes engineers to optimize for the wrong things without considering a healthy team's operational reality. Project management at it's core, is about facilitating communication between team members of various skills and disciplines to pursue meeting goals as a group. The goals may be business goals, personal development, team building, but whatever the goal the flaw of orienting your software development processes around them is that they fail to recognize the operational truth of any project.

Driving, _is a verb_. That means that -- in general English -- it requires both an actor and object. The object in all cases is _development_ work, but none of the `x-Driven Development` have a proper actor. Only objects driving the project. _This is a problem_! You need _actors_ driving the process, shaping the process, building the process.

It further compounds as it hides the fact that the drivers of a software project _switch around constantly_. Sometimes it's the budget folks that need to drive things, sometimes security and compliance, sometimes it's the raw implementation, other times the platform. Software projects have many attributes and factors that are required for success and masking this with poor fascades that shape the process (usually at the expense of the people in the system) makes them broken processes by default.

With this in mind, along other things that have gotten me thinking a lot about project management, I present my brief Manifesto for Developer Driven Development

## Developer Driven Development Manifesto

This is a collection of the best patterns for software development that I can come up with as someone that has been doing this for many years. It is not a hard process and if anyone comes trying to sell DDD consulting, kindly [redacted explicit language] show them the door.

There are three main goals of Developer Driven Development

- Everyone is a developer
- Human Centric Tooling and Processes
- Clear Communication

### Everyone is a Developer

This might be a smidge contentious, but I think that Software Developer is not a job title or role, but more of a organizational term. Anyone that works at and contributes to a software project (be it as an engineer, manager, producer, stakeholder, operator, etc) all contribute to the development of the software, they simply have different roles.

The only people inside your organization who are not developers are those that do not contribute to the development process (such as folks strictly in an administrative and accounting roles that might not interact with the software process).

### Rules

- Metrics are kept secret
- Emphasize Agency not beaurocracy
- Flexibility in implementation, not criteria
- Capture everything
- Specs/Knowledge/Goals/etc should be in Version Control Systems

### Core Mechanisms

The core mechanics of any good development is establishing criteria and validation of those criteria.

**Driver**: The person in charge of setting and/or validating the criteria of a project layer.
**Deliverable**: Anything result of work (can be research, maintenance,
**Criteria**: Can be a goal, compliance, desired outcome, specification, whatever
**Validation**: How the criteria is evaluated that is has been met.

#### Examples

Deliverable: Customer data management portal.
Criteria: Must adhere to <insert privacy> regulations
Validation:

Deliverable: Application Deployment of Customer dta manage
Criteria: Customer Data must be physically isolated from one another
Validation: Provably sandbox the tenants in different physical airgapped servers. (Probably some additional penitration testing as well)

Criteria: Must support <insert specific technology>
Validation: Suite of E2E and user tests that demonstrate compatibility.

These mechanisms propagate up and down the layers of

### Layers of Organization

While the above core mechanics are useful, they don't mean much without some kind of structure and flow. DDD is a layered way to organize with different primary roles in each

#### Layer 1 - Project

Primary Drivers: Stakeholders/Project Managers

What happens here: Specifications, business goals, high level requirements and other deliverables.

Conceptually, the project is just a container for every other aspect of the project. A wrapper if you will.

In practice, this should be where business goals, compliance requirements, desired functionality, etc are decided and negotiated upon. It specifically involves the direct stakeholders and project owners/managers goals, and other specific requirements and goals are desired. These get filtered down into Epics that can be further broken down and tackled by individual teams or team members depending on complexity.

Deliverables: Definition of the epics and milestones for a project.

##### Milestones

Milestones consist of distinct deliverables for a project that belong to a phase. The contents of a milestone will typically be the epics that are defined during the project phase. belong to the project as ways to organize progress. Milestones should remain flexible to add and remove deliverables/epics as the schedule and requirements require. They also do not necessarily represent a fixed point in time (though scheduling may be desired).

#### Layer 2 - Epics

Primary Drivers: Tech Leads/Project Managers

Epics define the goals and outcomes required to meet a deliverable (or a portion of a deliverable) while adhering to the criteria. Epics _should_ typically reflect a single set of deliverables for the project (typically in the form of a prototype, feature, or research). Epics should typically be locked until completion with few alterations. If done correctly an epic should be completable within a few sprints and -- while adjustments and fine tuning are typically fine -- shifting goals and priority are can cause a lot of development work to need to be redone. As a result, it is usually better to cancel an epic that needs major readjustment and move to a v2.

(Be pragmatic, if you can salvage work from a prior revision like DB schema, backend services, kubernetes manifests, etc. Do so)

In short, lock epics, additional scope should be in follow on epics, small adjustments can be included, big adjustments should cancel and reschedule.

Epics themselves are the guides for the Individual Contributors to build out the software according to the guidance while giving visibility into the actual progression and burndown of the tasks and features in your project.

#### Layer 3 - Task

Primary Drivers: Direct Individual Contributors

Defines an Atomic unit of work. i.e. a distinct task required to contribute towards the completion of an epic. While there are limits to the granularity of the task, the goal is that tasks are disposable and ephemeral

Tasks should be kept incredibly granular. Almost to the point of excess, in that you can toss and assign the tasks to anyone on your team with a relevant skillset. (Note: If well defined, certain tasks could be picked up by AI Agents).

For software specific tasks, the ideal situation would be creating a feature branch for the epic and having each task contribute a single Commit/PR to said branch (or PR stack if you are fancy) However, pragmatism wins over process so your team should use their brains to make sure that the number of PRs are manageable for review (read your code, don't be dolts like anthropic).

The goal of a task is to ensure that you can have fine grained tracking of each task. Important to note that each task should be scoped to at least 30 minutes to an hour of work, there's no need to add excessive numbers of tasks to track each individual module change (unless it spans a completely different code base), but it should be sufficiently broken down that the necessary work is clear and concise.

Each task should have their dependencies or questions mapped out so that a separate task may be created and they are viewed as disposable so that we can easily track blockers, investigations, etc. Time is tracked against tasks and collated against epics.

Tasks also should be actively created as new work is scoped. An epic may start with a single task of "Scope and investigate required changes to meet goals" which could spawn a single task or a dozen tasks depending on the complexity.

#### Note on Long term Operations

Operations can use a lot of the same, features of a project, except that they should orient themselves around the long term operating capacity and maintenance of the system. Operational concerns are specifically about sustaining and supporting the software platform and business and adding value to the business through stability and reliability. I'd still recommend tracking

### Flow

In any good process it's important to understand how things flow. So here's a diagram that hopefully demonstrates

```mermaid

Put a diagram of the flow

```

The idea is that each layer is responsible for iterating on their own portion of the development process. Much of the discussions with stakeholders will iterate on the requirements and criteria through meetings discusssions and outline the technical and non-technical requirements.

These filter into epics where each goal/requirement/criteria is given their own sets of tasks, operational criteria, investigations, deliverables, etc.

Which then get iterated through a large series of tasks. Tasks can spawn new tasks, and may influence the design of an epic, but the feedback items should flow up through each layer. There should always be a path for post epic review to allow further feedback about technical requirements, implementation requirements, future requirements, etc to propagate back up the organization.

Specifically, tasks are probably the most critical part of this as the tasks themselves are what build context for the actual construction and delivery of the softare. It also tracks the development and time spent on tasks.

### AI Augmentation

Unlike most "automated software factory" approaches, I think this process is strongest for building with LLMs because LLMs when paired with a proper CLI are amazing at the repetetive nature of posting notes, ideas, and creating/tracking time against tasks. The biggest flaw with my current management philosophy is the amount of times you might get stuck sitting creating new tasks just to execute them instead of working on them.

## Final Notes

I'll refine this later, for now I'm putting this whole process into practice with my personal workflows using Aventask to keep myself working on things. I'm sure it's not flawless, but it's at least an honest, human centric system that provides agency to engineers.
