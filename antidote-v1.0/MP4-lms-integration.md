# MP4 - Integration with External LMS

> **WARNING What follows below is very temporary**. These are very rough notes taken over a long period of time from a variety of sources. I'm pushing it raw so that I can stop holding these scratchpads on my computer. Please do not view anything below as particularly accurate, or even needing to be done. This will get re-organized very soon.

## LMS Candidates

- https://docs.moodle.org/dev/Core_APIs#Access_API_.28access.29
- https://www.fun-mooc.fr/
- https://open.edx.org/the-platform/

#### Misc Notes from old opt-in progress tracking design - maybe not useful

Set up and deploy HA StackStorm https://docs.stackstorm.com/install/k8s_ha.html
Probably need to use external Mongo and Postgres
Discord pack and perhaps even chatops integration. Depends on use cases.
Discourse pack
Antidote-Gamification Pack
Re gamification, we need two things - a place where users experience the gamification (discourse primarily but also notifications to discord and even twitter), a source of truth for who has what rewards (also discourse)
Some kind of engine that determines when to give out rewards (stackstorm with our gamification pack)
Add linked accounts like GitHub so we can easily track contributions and add badges
In general, tracking who has done what is super important. This will provide the foundation for stuff on top of that, but you have to have this truth stored properly.

