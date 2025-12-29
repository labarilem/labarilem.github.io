---
title: "Building Hacker News Games"
excerpt: >
  How I built a videogames catalog from HN submissions and the idea behind it.
date: 2025-12-27
last_modified_at: 2025-12-28
categories:
  - My Projects
tags:
  - Games
share: false
---

A few months back I started thinking about a simple idea: *“Wouldn't it be cool to browse games made by the Hacker News community in one place?”*. So I slowly started building [Hacker News Games](https://hackernews.games/), my latest small corner of the web.

## A library of tiny worlds

On HN, people often share interesting, original and creative small videogames. I've stumbled upon very interesting short games several times.

Over time, though, those links accumulate and vanish into the noise. You catch a fun game, but a week later it's buried and hard to rediscover.

HN Games tries to fix that. It's a **catalog of community-made games** you can browse, search, and actually *play* without digging back through years of HN threads. Right now there are hundreds of games in the catalog, and I'm still adding more. My goal, of course, is to eventually catch up to today's games and then keep the catalog updated as new games are posted to HN.

You'll find things like:

- Small experimental word games  
- Classic ports (sometimes running in WASM)  
- Puzzle games, simulations, and strange one-off ideas  
- Projects that never got much attention but are genuinely fun  

It's not a random dump. Games are **manually curated** to keep the quality high (or at least not too low). I try to keep only playable titles, usually free, and accessible without having to clone a repo or compile code locally.

## From idea to open source project

Both the code and data powering the website are open and can be found on [GitHub](https://github.com/labarilem/hn-games).

The project is built with TypeScript, React, Tailwind and Next.js. It's hosted on a Vercel free plan. On the technical side, I'm focusing on development simplicity and keeping costs low. I enjoyed designing the data pipeline to improve DX for myself and to keep the project from feeling like tedious work.

The data workflow looks roughly like this:

1. Scrape Hacker News posts over a given time range.
2. Manually filter scraped posts to isolate actual games from noise.
3. Manually enrich and curate valid entries with metadata. I add info like genre, platform, links and I take screenshots too. At this point, I have a JSON file that acts as static database.
4. Compile the JSON dataset into a TypeScript file used to serve data to the frontend. I also statically compute some stats about the catalog at this point.

There are also utility scripts to:

- Check for broken/dead links
- Refresh HN scores of latest submissions
- Check for duplicate games
- Check for images and metadata consistency across the catalog

The whole process is *nicely balanced between automation and human judgment*. Machines do the boring parts, **I make the final call**.

## Designing the UX

I'll be honest: *UI and interaction design aren't my strongest skills*.

To move faster, a good portion of the early UI and layout work was LLM-assisted. That helped me skip the blank-page problem and focus on iteration instead of perfection. I'd push a change, browse the site, notice friction, and adjust.

I went for a dark material-like theme, inspired by [Crazy Games](https://www.crazygames.com/).

Filtering and sorting are essential, and they can be done directly from the home page with several options. Every game has a preview screenshot that allows you to quickly spot something you might like.

The goal is simple: **make discovery feel lightweight**. You should be able to land on the site, click something weird, and be playing within seconds.

I also added a [/random](https://hackernews.games/random) page to play a random game and a [/rip](https://hackernews.games/rip) page to check out decommissioned games, if you're looking for nostalgia. 

## Adding a newsletter (without overthinking it)

At some point I realized I wanted a low-effort way to share updates: newly added games, small site changes, or interesting finds from the catalog. That's how the [HN Games newsletter](https://hackernews.games/newsletter) came to life.

I built it using [Kit](https://kit.com/). I deliberately chose it because it's free, quick to set up, and it provides a valid physical mailing address, which makes dealing with newsletter legal requirements much easier without extra work or cost.

The newsletter is intentionally lightweight. No growth hacks, no weekly promises. Just occasional updates when there's something genuinely worth sharing. It fits the spirit of the project: small, useful, and respectful of people's inboxes.

This is also an experiment that helps me gauge interest in the project. If not too many people use it, I may choose to remove it in the future.

## Shipping a small thing to the internet

HN Games isn't meant to be a massive platform. It's intentionally small, opinionated, and focused. It exists because I wanted a place where these cool games didn't disappear.

There's something quietly satisfying about building a tool that showcases other people's creativity. Even more so when it's open source and anyone can improve it, reuse the data, or fork the whole thing for their own experiments.

If you've built a game that belongs in the catalog, or you spot something that shouldn't be there, contributions and feedback are very welcome.

Thanks to HN Games, I learned that **sometimes the best side projects are the ones that simply organize joy**.
