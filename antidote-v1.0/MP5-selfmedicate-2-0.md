# MP5 - Self-Medicate 2.0

> **WARNING What follows below is very temporary**. These are very rough notes taken over a long period of time from a variety of sources. I'm pushing it raw so that I can stop holding these scratchpads on my computer. Please do not view anything below as particularly accurate, or even needing to be done. This will get re-organized very soon.


- Web UI that includes wizards for building images, as well as lessons (which can use the available created images). This would result in a PR, auth'd with github
- Workflows in stackstorm for standing up a preview site. Could optionally trigger on PR creation, or even W
- Image build workflows that integrate with MP3

In this project we'll add a lot of useful tooling on top of selfmedicate to make it even easier to develop lessons. Roughly this will include

- Lesson building and editing in the browser
- Enhanced boostrapping functionality
- Integration with git for easy PRs
- Troubleshooting

We were previously thinking about hosting this, but this would be cost-prohibitive. Rather, it makes the most sense to develop an "Antidote Editor" application that can be deployed on top of the existing selfmedicate tool to make it less sharp-edged.

Details TBD

## Candidates for the editor portion

https://ace.c9.io/#nav=embedding
https://github.com/janitortechnology/janitor
https://aws.amazon.com/cloud9/?origin=c9io
https://github.com/theia-ide/theia

## Old Notes about Centralization (we will likely not do this)

Two problems:
Selfmedicate is hard to debug unless you know a lot of stuff. Most of our contributions will eventually come from people who barely know how to automate, so we have to lower the bar here.
https://janitor.technology/projects/





Make a /build part of the site.

Say they use that to bootstrap a new lesson, what then? We can do some basic bootstrapping and validation, but at some point we have to 

Run Antidote, just like we do in selfmedicate, so they can see it running
Get their new contribution into Github as simply and easily as possible. Do we ask them to sign in via GIthub or something? Maybe Antidote Build is some kind of Github app?

Need to get this integrated with the rest of the 1.0 plan becuase this will be big. It’s already kind of in there but you need to add more details and timelines.


