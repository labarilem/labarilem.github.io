---
title: "Vibe Coding from my Smartphone"
excerpt: >
  How I built Collatz Lottery from my smartphone during my holidays.
date: 2025-08-27
last_modified_at: 2025-08-28
categories:
  - My Projects
tags:
  - Math
share: false
---

I recently built [Collatz Lottery](https://collatz-lottery.marcolabarile.me/). It's a fun project about the notorious [Collatz conjecture](https://en.wikipedia.org/wiki/Collatz_conjecture), where you try your luck finding a counter-example to the conjecture.

It's not uncommon for me to have ideas about small and *sometimes* fun projects. But usually I let them sit 
in my Trello backlog until I lose interest, since I don't have much time to work on them.

So when I found out that [Copilot agents can asynchronously work on GitHub issues](https://docs.github.com/en/copilot/how-tos/use-copilot-agents/coding-agent/assign-copilot-to-an-issue), I immediately thought about my micro side projects. Is this a way to get the ideas out of my mind and into the web?

The plan was to set up a new repository and work on it exclusively via GitHub issues assigned to Copilot. **All from my smartphone**, using the GitHub mobile app. And why not work on this **only during my holidays**. So I set up a Vercel deployment to check out the result and be able to provide some feedback to Copilot. Vercel has been quite helpful with automatic preview deployments for pull requests, as it allowed me to test the changes in each PR before merging into the main branch.

Everything went smoothly and it took [11 issues](https://github.com/labarilem/collatz-lottery/issues?q=is%3Aissue%20state%3Aclosed) to complete the project. Clearly the focus wasn't on code quality nor on performance. **Just getting the code out** to build and publish my idea. And I can say I'm pretty satisfied with the outcome! Will definitely try this approach again for simple web apps, *hoping I still get ideas now that there are fewer excuses for not building them*.
