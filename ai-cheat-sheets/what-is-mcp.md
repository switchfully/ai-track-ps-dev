# What Is MCP, and Why Are We Using It?

In Block 3, you'll connect your AI tool to a "hosted MCP server." This page explains what that
actually means before you get there.

## The problem MCP solves

An AI model like the one behind Copilot or Claude doesn't know your company's specific coding
standards, API contracts, or QA standards unless you tell it. Left alone, it will guess based on
general patterns it's seen elsewhere, which is exactly how you get confidently wrong answers:
plausible-sounding code that violates a standard nobody told the AI about.

The obvious fix is: give the AI the actual document. You could paste the whole standards
document into a chat every time, but that's tedious, easy to forget, and doesn't scale once you
have several documents the AI might need.

## What MCP actually is

MCP (Model Context Protocol) is a standard way for an AI tool to ask an external source for
information on demand, instead of you pasting it in by hand. Think of it as a plug shape: any
AI tool that "speaks MCP" can connect to any server that "speaks MCP" and use whatever it
offers, the same way any device with a USB-C plug works with any USB-C port, regardless of who
made either one.

Concretely, in this workshop:

- We run an **MCP server** that has access to a small set of documents: the Stackcraft coding
  standards, the QA standards, and the Meridian regulatory standards.
- That server exposes two capabilities (called "tools" in MCP terms): one to list which
  documents are available, and one to read a specific document's content.
- Your AI tool (Copilot, Claude Code, or Claude.ai) acts as an **MCP client**. Once connected,
  it can call those tools itself, mid-conversation, whenever it decides it needs to check
  something, without you having to paste anything.

## Why this matters for Block 3

The exercise is specifically designed to show the difference between an AI answering from
general knowledge (which can be wrong or invented) and an AI answering after actually reading
the real document. Once connected, you can ask something like:

> "Does our coding standard say anything about how we handle null checks on repository
> results?"

and the AI will genuinely go read `stackcraft-coding-standards.md` through the MCP connection
before answering, rather than guessing. You'll be able to tell the difference, because it can
quote the actual document, and because you can deliberately test it by asking about something
the document does or doesn't cover.

## Connecting to it

The exact steps depend on your tool:

- **VS Code / IntelliJ / Visual Studio with Copilot:** see `copilot-in-your-ide.md` for the
  MCP configuration path in each.
- **Claude Code:** `claude mcp add` from the terminal, pointing at the server URL your coach
  provides.
- **Claude.ai (browser, no install):** Settings → Connectors → Add custom connector, paste the
  server URL. This is the same underlying protocol, just accessed through a browser instead of
  an IDE.

Ask your coach for the exact URL on the day, it's specific to this workshop's deployment.

## The one-sentence version

MCP lets your AI tool read a real document when it needs to, instead of guessing from memory,
and Block 3 is built to make the difference between those two obvious.
