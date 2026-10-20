# Working efficiently: instructions, prompt files, skills and agents

Most of today is about using AI carefully. This sheet is about using it efficiently: not typing the same rules into every single chat, and turning the things that work into something you (and your team) can reuse.

## Why bother?

By lunchtime, you'll notice you keep writing the same lines over and over: "use constructor injection", "run the tests before you say you're done", "don't touch the existing tests". Forget one of them once, and the AI quietly falls back to its own habits. Retyping them is slow, and it only works as long as you remember to do it.

There are three ways to store those rules. We'll go from simple to more powerful.

## 1. Instructions file

An instructions file is a short text the AI reads before every request you make in a project. It's perfect for house rules: the stack, how you write code, how you write tests, what to do before saying "done". Keep it short, 10 to 20 lines is plenty.

Where do you put it?
* GitHub Copilot: a file in your repo, `.github/copilot-instructions.md`. It applies to every request in that workspace, for everyone who opens it.
* Claude Code: a file `CLAUDE.md` in the root of your repo. Claude Code reads it at the start of every session.
* Other tools have their own variant. Many of them also read an `AGENTS.md` file.

> The rules live inside the project, right next to the code, and everybody gets the same ones. That's why this is the first thing you set up on a new project.

## 2. Prompt files

A prompt file is a prompt you use a lot, saved so you can start it with a short command instead of typing it out again. In GitHub Copilot, that's a `.prompt.md` file in `.github/prompts/`. You start it by typing `/` followed by its name in the chat. In Claude Code, the equivalent is a markdown file in `.claude/commands/`.

## 3. Skills

A skill is a folder with a `SKILL.md` file in it:

```
stackcraft-audit/
└── SKILL.md
```

And this is what the file looks like:

```
---
name: stackcraft-audit
description: Audits a Java project against StackCraft's coding standards. Use when asked
  to review, audit or check code against company standards.
---
1. Read the standards through the ai-track-standards MCP server...
2. ...
```

The AI only reads the name and the description of each skill it has. When your request matches a description, it loads the full instructions of that skill. This means:
* You can have a lot of skills without them cluttering every chat
* The description decides whether your skill gets used, so make it say what the skill does and when to use it
* A skill can contain more than one file: a template, a checklist, a script
* You can share a skill with your team, it's just a folder in the repo

How do you install one?
* GitHub Copilot in VS Code or a Codespace: save it as `.github/skills/<skill-name>/SKILL.md` in your repo. Copilot picks it up when a request matches, or you start it yourself with `/<skill-name>`. If it doesn't show up, check that agent skills are switched on in the VS Code settings.
* Claude Code: save it as `.claude/skills/<skill-name>/SKILL.md`.
* Not sure your IDE supports skills? Open the repo in a GitHub Codespace, that's VS Code.

## So which one do I use?

* Rules that should apply to everything you do in this project? Instructions file.
* A task you start yourself, the same way every time? A prompt file, or your prompt library.
* A task the AI should recognise on its own and then do your way? A skill.

## A few habits that save you time with agents

* **Let it run the tests.** An agent that can run `mvn test` and read the output fixes its own mistakes. An agent that can't, guesses.
* **Keep the tests off limits.** Tell the agent that it never changes a test to make it pass. A stuck agent will otherwise sometimes do exactly that.
* **Plan first, then execute.** Ask for a plan and read it before the agent changes a file. Then let it work in steps, and let it stop after each step.
* **Read the diff, not the summary.** The agent's own summary of what it did can leave things out. The Source Control panel can't.
* **Check what it adds.** A new dependency, a new plugin, a new version: look it up before you trust it.
* **Start a new chat when you switch topics.** A long chat drags old context along, and the answers get worse. Only bring what's needed: the files and where you are right now.
* **Keep what works.** Good result? Save the prompt. Bad result? Add a line to your instructions so it doesn't happen again.

> One last warning: instructions and skills make AI faster, not more correct. A skill that says "quote the standard" makes it more likely the AI quotes the standard. It doesn't guarantee it. You still check.
