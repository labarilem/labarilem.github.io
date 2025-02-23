---
title: "PostgreSQL Fixtures Editor"
excerpt: >
  I built a VS Code extension to make editing PostgreSQL fixtures easier. Here's why I made it, how it works, and how you can use it.
date: 2025-02-23
last_modified_at: 2025-02-24
categories:
  - My Projects
tags:
  - VS Code
  - PostgreSQL
share: false
---

When working with SQL databases, managing fixture data can be a hassle when the database schema changes. Whether you’re writing tests, preparing demo environments, or just tweaking data, you often end up editing large SQL files by hand. LLMs too can’t help a lot in this scenario, because of context size limits and hallucinations. I found this frustrating, so I built a VS Code extension to make it better: [PG Fixtures Editor](https://marketplace.visualstudio.com/items?itemName=labarilem.pgfixtureseditor).

### How It Works

It allows you to programmatically edit SQL code in fixture files. Under the hood, it uses [pg-fixtures-editor](https://www.npmjs.com/package/pg-fixtures-editor), an NPM package I built specifically for automating typical transformations that SQL fixtures must undergo because of schema changes in the database. This package relies on [pgsql-ast-parser](https://www.npmjs.com/package/pgsql-ast-parser) to parse and emit SQL code in the Postgres dialect.

### Key Features

This extension adds 2 commands to VS Code:
- **Remove Column**: removes a column and its values from the selected INSERT statement(s).
- **Add Column**: adds a column and its value to the selected INSERT statement(s). Note that the value will be emitted “as is”, so you need to include proper quoting and escaping if needed. Example: to insert a SQL string, you need to pass ‘your string’ as columnValue (i.e. enclosed with single quotes).

Here’s an example of the Remove Column command in action:

![Remove Column command demo](https://raw.githubusercontent.com/labarilem/pg-fixtures-editor-vscode/main/images/demo-hires.gif){: class="center"}

### Future Plans

I built this extension mostly for myself, this is why I published it with a minimal feature set and it’s tailored only for PostgreSQL. I’ll see if other people will find it useful too and eventually evolve it from their feedback.

So please let me know what you think! Feature requests, bugs, ideas, etc. are all welcome in the project’s [Github issues](https://github.com/labarilem/pg-fixtures-editor-vscode/issues).