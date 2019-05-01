---
title: "An open source stack for developers' personal notes"
excerpt: "How to combine QOwnNotes, Git and mobile clients to build a cloud-based solution for personal notes."
preview:
  image: /assets/images/2019-04-27-personal-notes/qownnotes-editor.png
date: 2019-04-27
last_modified_at: 2019-04-27
categories:
  - Tools
tags:
  - Notes
  - Markdown
  - Git
qownnotes-editor-gallery:
  - url: /assets/images/2019-04-27-personal-notes/qownnotes-editor.png
    image_path: /assets/images/2019-04-27-personal-notes/qownnotes-editor.png
    alt: "QOwnNotes editor's UI."
    title: "QOwnNotes editor's UI."
fasthub-gallery:
  - url: /assets/images/2019-04-27-personal-notes/fasthub.png
    image_path: /assets/images/2019-04-27-personal-notes/fasthub.png
    alt: "Fasthub for Andoid UI."
    title: "Fasthub for Andoid UI."
qownnotes-git-configuration-gallery:
  - url: /assets/images/2019-04-27-personal-notes/qownnotes-git-configuration.png
    image_path: /assets/images/2019-04-27-personal-notes/qownnotes-git-configuration.png
    alt: "QOwnNotes git configuration."
    title: "QOwnNotes git configuration."
qownnotes-folder-configuration-gallery:
  - url: /assets/images/2019-04-27-personal-notes/qownnotes-folder-configuration.png
    image_path: /assets/images/2019-04-27-personal-notes/qownnotes-folder-configuration.png
    alt: "QOwnNotes folder configuration."
    title: "QOwnNotes folder configuration."
qownnotes-scripts-configuration-gallery:
  - url: /assets/images/2019-04-27-personal-notes/qownnotes-scripts-configuration.png
    image_path: /assets/images/2019-04-27-personal-notes/qownnotes-scripts-configuration.png
    alt: "QOwnNotes scripts configuration."
    title: "QOwnNotes scripts configuration."
---

Personal notes don't usually need scrupulous reviews, they are quick and dirty by nature. You just need to create them without too much effort and then access them from different devices.

So, in my opinion, a stack that lets you easily manage personal notes must include:

- A comfortable language and a lightweight editor. This combination lets you create new notes with speed.
- A remote storage system, in order to access your notes from different devices and have a reliable backup of your data.
- A way to download/upload your notes to the remote storage system.

Of course, the more open source components you are able to pick, the better.

### Language and editor

Markdown solves the problem of quickly writing formatted notes, I've been very happy with it. Another big advantage is that Markdown is just plain text, so it is compatible with virtually any text editor.

There are several open source notes editors that support Markdown, including:

- [Joplin](https://joplin.cozic.net/), an editor with modular synchronization capabilities.
- [Visual Studio Code](https://code.visualstudio.com/), lets you do _almost_ anything with the right extension.
- [Simplenote](https://simplenote.com/), an editor with a built-in synchronization feature. It's quick and easy to get started with Simplenote.
- [QOwnNotes](https://www.qownnotes.org/), an editor which formats markdown as you write it. This was the killer feature for my use case: it makes writing quick notes a real pleasure. Also, it has a scripting engine that can execute user-made scripts.

My choice has been QOwnNotes. I really like the idea behind the editor and its high degree of customization and extensibility.

{% include gallery columns=1 id="qownnotes-editor-gallery" class="" caption="QOwnNotes editor's UI." %}

### Storage and synchronization

There are several well-known storage services/protocols:

- [Google Drive](https://www.google.com/drive/)
- [Dropbox](https://www.dropbox.com/)
- [NextCloud](https://nextcloud.com/) server (can be self-hosted)
- [git](https://git-scm.com/) server (can be self-hosted)

Personal notes tend to be small files which mainly consist of text. You need to store a collection of those. Seems like a very good fit for git!

I'm using a private repository in my GitHub account to store my notes, but you can easily swap GitHub with your self-hosted git server.

Also, QOwnNotes has built-in support to commit changes to your notes in a local git repository.
To enable automatic commits in QOwnNotes:

_1)_ Navigate to `Settings -> General -> Git versioning` in order to configure the integration with git

{% include gallery columns=1 id="qownnotes-git-configuration-gallery" class="" caption="QOwnNotes settings for git." %}

_2)_ Navigate to `Settings -> Note folders` and check the _"Use git to keep a local history of your notes"_ option

{% include gallery columns=1 id="qownnotes-folder-configuration-gallery" class="" caption="QOwnNotes folder settings." %}

At this point, you can use some [QOwnNotes scripts](https://github.com/labarilem/qownnotes-scripts) I wrote to automatically push to a remote repository the commits made by QOwnNotes. These scripts completely automate the whole notes synchronization workflow.

**Notice** At the moment, I have tested these scripts only on Windows.
{: .notice--warning}

To add my scripts to your QOwnNotes installation:

_1)_ Download them from their [GitHub repository](https://github.com/labarilem/qownnotes-scripts)

_2)_ Navigate to `Settings -> Scripting -> Add script -> Add local script` and select the files you just downloaded. They will be added as scripts to QOwnNotes

{% include gallery columns=1 id="qownnotes-scripts-configuration-gallery" class="" caption="QOwnNotes scripts settings." %}

### Bonus: Mobile clients

Since QOwnNotes preserves files as plain text and git is just a protocol, __you are free to use any client that supports reading text files from a git repository__ to access your personal notes.

This means you can even use mobile git clients, such as [FastHub](https://github.com/k0shk0sh/FastHub) (which is also open source), to work with your notes from your smartphone.

{% include gallery columns=1 id="fasthub-gallery" class="" caption="FastHub for Android UI." %}

### To sum up

This is my stack of technologies for personal notes management:

- __Language__: [Markdown](https://www.markdownguide.org/)
- __Editor__: [QOwnNotes](https://www.qownnotes.org/) with git commits enabled and [scripts](https://github.com/labarilem/qownnotes-scripts) for automatic `git push` operations
- __Storage__: [GitHub](https://github.com/) account (you can easily swap this component with your self-hosted git server)
- __Synchronization__: [git](https://git-scm.com/)
- __Mobile client__: [FastHub](https://github.com/k0shk0sh/FastHub) for Android

This stack is developer-friendly, free and open source. Try it out!