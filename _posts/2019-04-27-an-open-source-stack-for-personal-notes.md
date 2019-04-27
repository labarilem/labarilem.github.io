---
title: "An open source stack for developers' personal notes"
excerpt: "How to combine QOwnNotes, Git and mobile clients to build a cloud-based platform for personal notes."
date: 2019-04-27
last_modified_at: 2019-04-27
categories:
  - Tools
tags:
  - Notes
  - Markdown
  - Git
hs-gallery:
  - url: /assets/images/2018-06-01-my-notes/hs-1-full.jpg
    image_path: /assets/images/2018-06-01-my-notes/hs-1-small.jpg
    alt: "Gödel's incompleteness theorems."
    title: "Gödel's incompleteness theorems."
---

I'm maintaining a [repository of notes](https://marcolabarile.me/notes/) about topics I'm studying/I've studied so that I can introduce those topics to newcomers by simply sending them a link. So these notes need to be very clear and readable, possibly enriched with illustrations. Also, I review these writings several times before publishing to make sure there are no mistakes, both in spelling and exposed concepts.

### But what about personal notes?

I don't need to review the style and format of my personal notes because, well, those are private and I don't intend to ever release them. Personal notes are quick and dirty.

In my opinion, a stack that lets you easily manage your personal notes must include:

- A comfortable language and a lightweight editor. This combination lets you create quick and dirty notes with speed.
- A remote storage system, in order to access your notes from different devices.
- A synchronization mechanism to upload/download your notes.

Of course, the more open source components you pick, the better.

### Language and editor

Markdown solves the problem of quickly writing formatted notes, I've been very happy with it.

There are several open source notes editors that support Markdown, including:

- [Joplin](https://joplin.cozic.net/), an editor with modular synchronization capabilities.
- [Visual Studio Code](https://code.visualstudio.com/), lets you do anything with the right extension.
- [Simplenote](https://simplenote.com/), an editor with a built-in synchronization feature.
- [QOwnNotes](https://www.qownnotes.org/), an editor which formats markdown as you write it. This was the killer feature for my use case: it makes writing quick notes a real pleasure. Also, it has a scripting engine that can execute user-made scripts.

My choice has been QOwnNotes, I really like the idea behind the editor and the extensibility capabilities.

### Storage and synchronization

There are several well-known storage services/protocols:

- [Google Drive](https://www.google.com/drive/)
- [Dropbox](https://www.dropbox.com/)
- [NextCLoud](https://nextcloud.com/) server (can be self-hosted)
- [git](https://git-scm.com/) server (can be self-hosted)

Personal notes tend to be small files which mainly consist of text. We need to store a collection of those. Seems like a very good fit for git! I'm using a private repository in my GitHub account to store my notes.

Also, QOwnNotes has built-in support to commit changes to your notes in a local git repository. Then I wrote some [QOwnNotes scripts](https://github.com/labarilem/qownnotes-scripts) to push automatically those commits to a remote repository. You are free to add them to your installation of QOwnNotes, thus automating the whole notes synchronization workflow.

### Bonus: mobile clients

Since QOwnNotes preserves files as plain text and git is a client-agnostic protocol, you are free to use any client that supports reading text files from a git repository to access your personal notes.

This means you can even use mobile git clients, such as [FastHub](https://github.com/k0shk0sh/FastHub) (which is also open source), to work with your notes from your smartphone.

### To sum up

This is the stack for personal notes that I'm using:

- __Language__: [Markdown](https://www.markdownguide.org/)
- __Editor__: [QOwnNotes](https://www.qownnotes.org/) with git commits enabled and [scripts](https://github.com/labarilem/qownnotes-scripts) for automatic `git push` operations
- __Storage__: [GitHub](https://github.com/) account (you can easily swap this component with your self-hosted git server)
- __Synchronization__: [git](https://git-scm.com/)
- __Mobile clients__: [FastHub](https://github.com/k0shk0sh/FastHub) for Android

It's developer-friendly, free and open source. Try it out!