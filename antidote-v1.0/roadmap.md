# Antidote v1.0 Roadmap

We created NRE Labs in a very short amount of time, with the express intention of generating buzz early. This timeframe meant we had to take many shortcuts with how the platform scales and functions. This tradeoff has largely been worthwhile, as the buzz is now quite substantial.

However, what got us here, won’t get us there. To compare to the MCU - we’re nearing the end of Phase 1. We’re about to have our “Avengers” moment. And Ed Norton was a great hulk, he really was - but we need Mark Ruffalo if we’re going to pull this thing off long-term.

# Purpose of this Document

This document is meant to summarize all of the design work that is being considered so that it is targeted for specific release and dates. The details of the design work will be in other documents linked from here. This document will be the one-stop shop for knowing when the work will be done.

The work is organized into mini-projects, each of which has a section in this document outlining the milestones in each mini-project. Top-Level Execution Plan is responsible for assigning these milestones, and any other work that’s considered part of the v1.0 plan, to specific releases and dates.

It is my hope that this document creates space for others to join the “platform team”, and generally provides insight throughout the community and the rest of the organization into what’s going on with the platform in the next few months.

# Mini-Projects

These are mini-projects which will have their own sub-tasks to be executed in the plan later. We should first outline what will be required in each mini project, and link to subtasks in the execution plan below.

In general, these mini-projects should be done in sequence. There will be some natural overlap, but they are
ordered in terms of not only priority, but also complexity, and there are some hard dependencies between
them, which will be called out in separate documents.

## MP1: Syringe Re-architecture

This effort has three objectives:

- Make Syringe more resilient and scalable
- Make it easier to reason about and contribute to Syringe components
- Decrease the effort involved to integrate external systems with Syringe

The [MP1 doc (still under review)](https://github.com/nre-learning/proposals/pull/3) contains the proposed design information and a
series of tasks for getting there. See that for more information.

## MP2: Antidote-Web Revamp

Detailed design doc/plan TBD

## MP3: Enhanced Endpoint Image Security and Build Automation

Detailed design doc/plan TBD

## MP4: Opt-in Progress Tracking

Detailed design doc/plan TBD

## MP5: Contributions in the Browser

Detailed design doc/plan TBD

# Top-Level Execution Plan

The following plan only includes major features on the road to 1.0. Each release will certainly have other features and bugfixes included as well, with the usual workload on top of what’s contained in this plan. This plan is only meant to capture the “big rock” features and enhancements needed to move Antidote to a 1.0 state.

This document should be consulted as part of the normal [Antidote release planning process](https://antidoteproject.readthedocs.io/en/latest/releases/releaseplanning_platform.html), as a source for
tasks that could be placed in a release plan. It is not up to this document to specify when certain platform
releases should take place - rather, the intention here is to provide guidance on which specific release milestones should contain which v1.0 task.

What will follow is a series of minor release versions, with a list of features that should be implemented at each stage. Some of these tasks will come from one of the aforementioned miniprojects, others are standalone features that are self-contained but still viewed as required for v1.0 of the platform.

## v0.5.x

- [Feedback Box on every page](https://github.com/nre-learning/antidote-web/issues/76)
- [Support for Optional Objectives](https://github.com/nre-learning/proposals/pull/6)

## v0.6.x

- Embeddable Lessons

## v0.7.x

- Add “quiz” resource type

## v0.8.x

- Foo

## v0.9.x

- Foo

## v1.0.0

- Foo