---
title: "Building an MCP server for Brainfuck"
excerpt: >
  How I built an MCP server that exposes a Brainfuck interpreter and debugger as a set of tools for LLMs.
date: 2026-01-17
last_modified_at: 2026-01-18
categories:
  - My Projects
tags:
  - LLMs
share: false
---

I recently built a small MCP server called [brainfaq-mcp](https://github.com/labarilem/brainfaq-mcp), a tool that lets language models execute and debug [Brainfuck](https://esolangs.org/wiki/Brainfuck) programs through the [Model Context Protocol](https://modelcontextprotocol.io/docs/getting-started/intro). This post is a short write-up of why I built it, what it does, and what I learned along the way.

### Inspiration

The idea started after watching a [video](https://www.youtube.com/watch?v=TJ6ruN-o0PA) by [antirez](https://antirez.com/latest/0) (you might know him as the creator of Redis) where he explores a simple but powerful concept: **give an LLM the ability to see the results of its own actions**. Instead of just generating text, the model should be able to act, observe the outcome, and adjust its reasoning based on real feedback.

That idea stuck with me. I wanted to try it in a very constrained environment, where the state is small, explicit, and easy to inspect. *Brainfuck felt like a perfect fit*. It has only eight instructions, a simple memory model, and no abstractions to hide behind. If a model can reason about Brainfuck by observing concrete execution state, that is already a meaningful step beyond pure one-way text generation.

### What brainfaq-mcp does

The MCP server exposes a Brainfuck interpreter and debugger as a set of tools. An LLM can load code, execute it step by step, inspect memory, and read output, all through structured tool calls instead of simulated reasoning.

At a high level, the server provides:

* A way to load Brainfuck code and reset execution state
* Step-by-step execution, with control over how many instructions are run
* Full program execution until termination or input is required
* Runtime inspection of the memory tape, data pointer, instruction pointer, and output
* Input handling for programs that expect characters
* Safety checks such as memory bounds, configurable tape size, and overflow or underflow detection

The goal is not to be the best Brainfuck interpreter and debugger, but to be a transparent one for LLMs. **Every important part of the program state is observable**, which makes it suitable for debugging and reasoning tasks driven by an LLM. Of course no one builds production services with Brainfuck *(at least I hope so?!)*, but in case you need to hack some Brainfuck code for any reason, your favourite LLM will be able to help you with this MCP.

### Testing the server using itself

One of the more interesting parts of this project was testing. I wrote a test suite that exercises the interpreter through the same MCP interface that an LLM would use. Tests assert states resulting from a collection of particular Brainfuck programs. The suite is ~~copied~~ inspired by Daniel Cristofani's [Brainfuck test suite](https://brainfuck.org/tests.b).

A good example is the brainfaq-mcp [test](https://github.com/labarilem/brainfaq-mcp/blob/main/tests/test-bounds-left.test.ts) that checks what happens when the data pointer moves left past the beginning of the tape. This might be easy to get wrong and some interpeters have different behavior around this. I asked Copilot to fix the test by using brainfaq-mcp itself: it stepped through execution and inspected state, eventually figuring out issues and then it proposed different solutions.

Debugging failing tests in this way felt very aligned with the original inspiration. **The tool was effectively being used to observe and correct its own behavior.**

### Publishing it

After getting the server to pass the tests, I submitted it to a few MCP registries so others can find and experiment with it:

* [MCP Market](https://mcpmarket.com/) 
* [LobeHub Marketplace](https://lobehub.com/mcp)
* [modelcontextprotocol.io Official Registry](modelcontextprotocol.io)
* [Awesome MCP Servers](https://mcpservers.org/)

The idea is that anyone running an MCP-compatible client can quickly plug my MCP server in it.

### Closing thoughts

Model Context Protocol is an important building block in the LLMs world. Instead of baking tool logic directly into prompts or agents, MCP encourages a cleaner separation between models and capabilities. Tools describe what they can do, models decide how to use them, and both sides remain relatively simple.

This project captures a pattern I find compelling: let models interact with real systems, observe concrete state, and reason from actual outcomes rather than imagined ones. Brainfuck just happens to be a convenient playground for exploring that idea.

If this sounds interesting to you, please [check it out](https://github.com/labarilem/brainfaq-mcp) and let me know what you think!
