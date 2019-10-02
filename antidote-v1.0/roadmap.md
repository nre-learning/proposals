# Antidote v1.0 Roadmap

We created NRE Labs in a very short amount of time, with the express intention of generating buzz early. This timeframe meant we had to take many shortcuts with how the platform scales and functions. This tradeoff has largely been worthwhile, as the buzz is now quite substantial.

However, what got us here, won’t get us there. To compare to the MCU - we’re nearing the end of Phase 1. We’re about to have our “Avengers” moment. And Ed Norton was a great hulk, he really was - but we need Mark Ruffalo if we’re going to pull this thing off long-term.

This document is meant to summarize all of the work underneath the v1.0 umbrella and provide a one-stop shop for all information or relevant links per the progress towards that goal. It is my hope that this document creates space for others to join the “platform team”, and generally provides insight throughout the community and the rest of the organization into what’s going on with the platform in the next few months.

# Antidote v1.0 Work Breakdown: Mini-Projects

We've logically separated the areas of focus for moving Antidote to v1.0 into what we call "mini-projects", code-named MP1, MP2, and so forth. Each of these mini-projects should be handled separately, and there will be links below to find external documents that focus on specific design proposals and implementation milestones relevant to each mini-project.

This section will intentionally omit any discussions of timing - but in general, these mini-projects should be done in sequence. There will be some natural overlap, but they are ordered in terms of not only priority, but also complexity, and there are some hard dependencies between them, which we'll try to call out within each design document.

Each mini-project definition below contains:

- A brief explanation of the purpose of the MP
- A link to the proposed design and implementation plan (many of these are currently links to Pull Requests, and feedback on these during this stage is **crucial**)
- A link to the community forum category where broader discussions relevant to that MP can be held

## MP1: Syringe Re-architecture

**Proposed Design and Implementation Plan**: https://github.com/nre-learning/proposals/pull/3
**Discussion Forum** - https://community.networkreliability.engineering/c/antidote-platform-project-management/mp1-syringe-redesign

Since Syringe is the heart and soul of Antidote, we need to first focus on re-thinking Syringe's architecture
based on our long-term plans for the whole project. This MP has a few goals for redesigning Syringe:

- Make Syringe more resilient and scalable
- Make it easier to reason about and contribute to Syringe components
- Decrease the effort involved to integrate external systems with Syringe

## MP2: Antidote-Web Revamp

**Proposed Design and Implementation Plan**: https://github.com/nre-learning/proposals/pull/9
**Discussion Forum** - https://community.networkreliability.engineering/c/antidote-platform-project-management/mp2-antidote-web-re-vamp

Antidote-web was designed and written in a way that - while functional - has some big gaps with respect to commonly-held web and UX standards. This MP will accomplish a few things:

- Implement a common theme for all NRE Labs web properties including Antidote-web
- Re-design the whole thing with proper UX in mind
- Re-think the application side of things using a modern approach, maybe with an off-the-shelf framework
- Make it easier for other web developers to contribute.

## MP3: Enhanced Endpoint Image Security and Build Automation

**Proposed Design and Implementation Plan**: https://github.com/nre-learning/proposals/pull/7
**Discussion Forum** - https://community.networkreliability.engineering/c/antidote-platform-project-management/mp3-image-standardization

Using containers for Antidote Endpoints has its advantages, but there are also some downsides, especially in the area of consistency and security. This MP hopes to secure all Endpoints by creating a consistent, light, and easy VM-based framework for all endpoints. The endgame here is to provide the inherent security of VMs while maintaining the developer experience that comes from Dockerfiles. Everything as-code, as it is today.

## MP4: Integration with External LMS

**Proposed Design and Implementation Plan**: https://github.com/nre-learning/proposals/pull/10
**Discussion Forum** - https://community.networkreliability.engineering/c/antidote-platform-project-management/mp4-integration-with-external-lms

There are a number of Learning Management Systems (LMS) - some open source, some not - that would make sense to integrate with Antidote. Once MP1 is done, integrating external systems should be easier, and we may be able to provide some basic progress tracking functionality by integrating with one of these systems, as well as a slew of other advantages.

## MP5: Next-Generation Self-medicate

**Proposed Design and Implementation Plan**: https://github.com/nre-learning/proposals/pull/11
**Discussion Forum** - https://community.networkreliability.engineering/c/antidote-platform-project-management/mp5-self-medicate-2-0

Self-Medicate is a useful tool for not only deploying Antidote on a slim form factor, like a laptop, but is also a great companion tool for developing lessons. However, there are a few downsides to the current iteration:

- Hard to troubleshoot unless you know kubernetes
- It doesn't actually help you build content - just runs what you build, meaning you likely have to copy an existing lesson and modify it to suit your needs. Not ideal.
- Not everyone has a slick IDE set up on their machine.

This MP plans to create a new web-based application aimed at:

- Automated troubleshooting of local Antidote deployment
- Built in wizards for content creation
- Web-based editor

# Top-Level Execution Plan

This document should be consulted as part of the normal [Antidote release planning process](https://antidoteproject.readthedocs.io/en/latest/releases/releaseplanning_platform.html), as a source for tasks that could be placed in a release plan.

This document should **not** be viewed as the full plan for features in any release cycle - there are undoubtedly going to be features/fixes in each release not explicitly covered in this document. What we try to do here is provide guidance here on which platform versions should plan to incorporate which v1.0 milestones/features. Each platform release should combine this document's suggestions with the normal community triaging that's done at the beginning of each release cycle, to come up with the full list of features planned for that cycle.

What follows below is a series of minor release versions, with a list of features that should be implemented at each stage. Some of these tasks will come from one of the aforementioned miniprojects, others are standalone features that are self-contained but still viewed as required for v1.0 of the platform.

> Note that the below plan is WIP and subject to change all the time. As the mini-project drafts linked above become more finalized, it's almost certain that we'll be placing or moving individual MP-level milestones around in the below plan.

## v0.5.x

- [Feedback Box on every page](https://github.com/nre-learning/antidote-web/issues/76)
- [Support for Optional Objectives](https://github.com/nre-learning/proposals/pull/6)

## v0.6.x

- Embeddable Lessons
- Insert link to proposal for shared scratch dir in lessons

## v0.7.x

- Add “quiz” resource type

## v0.8.x

- TBD

## v0.9.x

- TBD

## v0.10.x

- TBD

## v0.11.x

- TBD

## v0.12.x

- TBD

## v1.0.0

- TBD