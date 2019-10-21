# MP1: Next-Generation of Antidote-web

> **WARNING What follows below is very temporary**. These are very rough notes taken over a long period of time from a variety of sources. I'm pushing it raw so that I can stop holding these scratchpads on my computer. Please do not view anything below as particularly accurate, or even needing to be done. This will get re-organized very soon.



#### Misc Notes

- The guacamole servlet functionality is the blackest box in the whole thing right now. I understand high level but tshooting is pretty impossible. This is still VERY MUCH in “PoC mode”
- I’m not a web designer. I’m sure I’m violating some standards
- I’m not a javascript developer, it’s possible I’m making it hard to extend unintentionally (i.e. might be better to use some kind of framework)

Pop up something in the web UI if there’s a new blog

User feedback right in the UI. Click this button or type in this box to tell us about a problem.
Responses go to some kind of queue for filtering and triage. Includes session and request IDs

Authentication and/or integration with Discourse for optional auth and gamification



Obvious links between Labs page and GitHub site--need to choose which page to make the “Home” page. 
Obvious link to Discord server
Resources tab: Blog, YouTube, Documentation, How to Contribute, Community Calendar, etc.
Discord: https://discord.gg/fRuSUyD (blocked in China)
Discourse: https://community.networkreliability.engineering/ (iffy in China - need to check)     
Mail lists:  Need to be recreated
Twitch -> YouTube
Eventually will need a Community tab. (For now: link to Discord). Need to set up mail list(s). Community wiki, to hold meeting agendas, notes, planning docs, etc.
Place to profile community leaders, maybe something like https://vuejs.org/v2/guide/team.html#active-core-team-members 
Rework web copy to be more community-centric, emphasize practical application of lessons and time to productivity.


Mobile compatibility
I like the katacoda abstract image they use of the split screen learning, but we could have a screenshot of a lesson instead of an abstracted image. The video is easy to use a thumbnail.These two things could easily be three short sections: 1. first (about the value prop) in-browser network automation learning as we describe with the words in the body of the home page now “Automation Unshackled” and the three boxes. that could be followed by a small part of logos of tools (see katacoda also) that we teach could be eyecatching (though I’d have some catchy text about not just wielding these tools but also automating the netops workflows around them ) 2. second a section that is a small part of two video thumbnails “watch an intro to NRE Labs” and “watch the show every week” (going to the live page) 3. a section about teaching and contribution.This would make the home page a little longer, but the home page is certainly the #1 ranking page of any site, so we may as well make good use of it.

Web front-end maturity, mobile enablement
Load balancing fixes including pinning and full understanding of guacamole traffic patterns (and debugging)
Long-term load balancer solution, including the move to websockets if needed
Does every connection spin up a new applet? Can you execute debug commands on the tomcat application?
Linting and overall better code quality for the JS side of things
Support VNC https://github.com/nre-learning/antidote-web/issues/65 (will likely also require syringe changes
Maybe split antidote-web from guac services? Need to understand this full architecture much better anyways. Maybe even think about something other than guacamole
Need to modify the nre site to look more like the labs site. Need the navbars to look the same and everything so the nre site and the labs site look the same. We need the ability to edit content more easily, while keeping the antidote-web application pure.
https://github.com/nre-learning/antidote/pull/44
Contributor page. Kind of like what github does, but with more of an nre-specific spin on it (details to lessons, etc)
"where to go from here" at the end of each lesson.
guest book concept for authors.
You should build a tool that can visualize dependencies in a curriculum
Write a blog or tweet that says "yeah.....this is the kind of complexity automation experts can take for granted, and exactly the kind of explicit mapping we aim to provide for you in NRE Labs
Remove the "Use the tabs below to use this lesson's resources" banner
simplify the buttons to be smaller, and hideable, especially on mobile
the mouse for guacamole sessions extends into the tab header area
the guacamole keyboard is overcontrolling - prevents us from using Ctrl+C and such for lesson guide content
See how easy it is to get mobile working. Collapse lesson guide or jupyter notebook into a tab
Copy + Paste still broken -  https://github.com/nre-learning/antidote-web/issues/58
Consider centralizing all the buttons and stage selector stuff to one side so it's easier to manage. Over the tabs perhaps.
Need to add a badge for collections if a lesson belongs to one. Click to go to it.

