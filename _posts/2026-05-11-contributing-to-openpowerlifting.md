---
title: "Contributing to OpenPowerlifting"
excerpt: >
  How my love for the sport turned into contributing to OpenPowerlifting.
date: 2026-05-11
last_modified_at: 2026-05-11
categories:
  - Open Source
tags:
  - Powerlifting
  - Data
share: false
---

I have been training in powerlifting for a year and just competed in a national federation meet, so [OpenPowerlifting](https://www.openpowerlifting.org/) is a meaningful open source project to me.
It is not just a website with rankings. It is also a public dataset that people use to analyze the sport, build tools, and even publish research. Seeing the list of [papers, presentations, and projects](https://openpowerlifting.gitlab.io/opl-csv/cited-in.html) built on top of the data made that very clear to me.

That combination is hard to beat: a project connected to one of my interests, useful to a real community, and close to "contributing to science and history" through better public data.

### Starting from a concrete problem

The part I ended up focusing on was the path from official meet results to OpenPowerlifting-ready data.

For the federation I worked on, [FIPL](https://www.powerliftingitalia-fipl.it/), the official results are published as PDFs. OpenPowerlifting, on the other hand, stores structured CSV files in a [public repository](https://gitlab.com/openpowerlifting/opl-data) with a specific schema. On paper that sounds straightforward: take the PDF, extract the data, clean it up, and submit it. In practice it is the kind of problem that gets messy very quickly.

Real meet results are full of edge cases. Sometimes a meet has multiple result PDFs that need to be merged. Sometimes names are formatted inconsistently. Sometimes divisions need interpretation. Sometimes there are small formatting issues that are easy for a human to overlook but matter a lot when the goal is high-quality structured data.

I did not want to solve that problem with a one-off script that only worked for the next meet. I wanted a workflow I could trust and reuse.

### Building tools instead of doing the same work twice

That is what led me to build my [openpowerlifting-tools](https://github.com/labarilem/openpowerlifting-tools) repo. The core idea was simple: if I am going to spend time understanding a federation's result format, I want to turn that understanding into code and tests so the next contribution is easier than the previous one.

I started building a parser for FIPL result PDFs and then wrapped it in a small set of scripts that make the whole workflow reproducible. A meet can be imported from the federation calendar, its PDF results can be downloaded and merged when necessary, and the parser can generate OpenPowerlifting-style output files.

I also released the parser as the [`@labarilem/opl-tools` NPM package](https://www.npmjs.com/package/@labarilem/opl-tools), so this workflow is easier for other contributors and maintainers to reuse without having to clone my repo and figure everything out from scratch.

To test the parser, I created a dataset with past meets from FIPL. For each meet, I keep a small folder with the source material and the expected outputs:

* `input.pdf` with the original results
* `URL` with the source links
* `meet.csv` with meet metadata
* `entries.csv` as the expected reference data
* `entries-parsed.csv` as the parser output

That structure made the whole project much more robust. Instead of wondering whether a parser change "probably still works", I can run it against a growing collection of real meets and compare the generated CSV against the reference CSV. If a change fixes one meet but breaks another, I find out immediately.

And because those tests are based on actual FIPL meets from 2025 and 2026, they are much better than synthetic examples I could have invented just for testing.

I like this setup because it turns a fuzzy data-cleaning task into something much more mechanical: reproduce, compare, fix, re-run. This makes the development process much more LLM-friendly too.

### From new meets to fixing old ones

One of the nice side effects of building the tooling was that it also made it easier to go back and improve older data.

Once I had scripts to compare parsed output with reference files and utilities to normalize columns or patch meet data, I started spotting issues in previously imported meets as well. Some of those fixes were tiny, like names or formatting. Others were more important, such as divisions, birth years, or lift attempts.

That is reflected pretty clearly in my [OpenPowerlifting merge requests](https://gitlab.com/openpowerlifting/opl-data/-/merge_requests/?sort=created_date&state=all&author_username=labarilem&first_page_size=20). A lot of them are not flashy feature work. They are careful data fixes: correcting names, cleaning divisions, adjusting entries, fixing bodyweight formatting, and repairing meet-specific mistakes that are much easier to catch once you have your own repeatable workflow.

I actually like that kind of contribution. It is not glamorous, but it is useful. Good public datasets get better through lots of small corrections that compound over time.

### Why this project clicked with me

There is also a more personal reason this project was a good fit for me: it sits at the intersection of things I already care about.

I love powerlifting. I love building tools. I love turning repetitive manual work into scripts. And I love projects where better engineering has a direct effect on the quality of a public resource.

OpenPowerlifting feels like infrastructure for the sport. People use it to check rankings, compare lifters, explore history, and analyze trends. Researchers use it too. So even small improvements to the data can have a wider impact than they might seem at first glance.

That made contributing feel especially rewarding. I was not just writing code for code's sake. I was helping improve something people genuinely rely on.

### Closing thoughts

This was a good reminder that open source contributions do not have to begin with some huge plan. Sometimes they start with a hobby, a repetitive problem, and the realization that if you automate the annoying part well enough, you can keep contributing with much less friction.

For me, contributing to OpenPowerlifting started exactly like that: with an interest in the sport, curiosity about how the data gets there, and a desire to make the path from official results to structured data faster, safer, and easier to repeat.

And honestly, that is one of my favorite kinds of project: something practical, a little nerdy, closely tied to a real-world community, and useful far beyond my own machine.
