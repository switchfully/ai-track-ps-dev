# GitHub Copilot: A Practical Starting Guide

GitHub Copilot is the primary AI tool used throughout today's workshop. If you've never used
it, or only ever accepted the odd autocomplete suggestion, read this first.

## The three things Copilot actually does

Copilot is not one feature, it's three, and knowing which one you're using matters.

**Inline suggestions.** As you type in your editor, Copilot suggests code, usually grayed out
ahead of your cursor. Press Tab to accept, Esc to dismiss. This is the feature most people
already know. It's useful, but it's also the least powerful part of Copilot, and it's not what
today's exercises focus on.

**Copilot Chat.** A conversation panel next to your code. You ask questions, describe what you
want, paste in error messages, or ask it to explain a piece of code. It responds in the chat
and can propose code changes, but doesn't apply them by itself unless you tell it to (or accept
a suggested edit). This is where most of today's work happens.

**Agent mode.** A more autonomous version of Chat. Instead of proposing one change and waiting,
an agent can read multiple files, make a plan, edit several files, run terminal commands (like
tests or a build), look at the results, and keep going until the task is done or it needs your
input. You review and approve as it goes. This is what Block 2 is about.

## Getting started with Copilot Chat

1. Open the Chat panel (usually a chat bubble icon in the sidebar, or a keyboard shortcut, see
   `copilot-in-your-ide.md` for where this lives in your specific IDE).
2. Type a question or request in plain language. You don't need special syntax.
3. Copilot responds with an explanation, code, or both. If it proposes a code change, you'll see
   an option to apply it, usually as a diff you can review before accepting.

A few built-in commands are worth knowing:

- `/explain`: explains the selected code
- `/fix`: proposes a fix for a selected error or problem
- `/tests`: generates tests for the selected code
- `/doc`: generates documentation comments for the selected code

You can also just describe what you want in your own words. The slash commands are shortcuts,
not requirements.

## Getting started with agent mode

Agent mode usually has its own toggle or mode selector inside the Chat panel (look for a
dropdown that says something like "Ask" vs "Agent" or "Edit" vs "Agent"). Once you're in agent
mode:

1. Describe the outcome you want, not just a single instruction. For example: "Add validation
   to the complaint submission endpoint so that a missing customer ID returns a 400 error with
   a clear message, then add a test for it."
2. Watch what it does. It will typically narrate its plan, then start editing files and running
   commands.
3. Review each proposed change before accepting it, especially the first few times. Don't just
   click "accept all" out of habit.
4. If it goes off track, stop it and clarify. A shorter, more specific follow-up instruction
   usually works better than a long correction.

## Writing prompts that actually work

The single biggest difference between a useless answer and a useful one is context. Compare:

> "Fix this."

versus

> "This test is failing with a NullPointerException on line 42. The `Customer` object is null
> because the repository returns null for unknown IDs instead of throwing. Fix the repository
> to throw a `CustomerNotFoundException` instead, and update the test to expect that."

The second version gives Copilot the actual problem, the root cause you suspect, and the
outcome you want. It will almost always produce a better answer.

Other habits worth building:

- Point it at the right files. If Copilot doesn't have the relevant file open or referenced, it
  will guess. Open the file, or mention it by name.
- Iterate. Your first prompt doesn't need to be perfect. Look at what it gives you, then correct
  it: "close, but use the existing `ValidationException` class instead of creating a new one."
- Ask it to explain its own output if you don't understand it. "Why did you do it this way?" is
  a completely normal follow-up.

## What to be careful about

Copilot will confidently produce code that compiles and looks reasonable, and is still wrong.
It doesn't know your business rules unless you tell it, or unless it's grounded against a real
source (see `what-is-mcp.md`). Specifically:

- Verify anything touching a business rule (timeframes, thresholds, validation logic) against
  the actual source document or standard, not just against whether the code looks sensible.
- Don't paste secrets, credentials, or customer data into a prompt.
- Read generated tests critically. A test that passes isn't automatically a test that checks
  the right thing.

## A note for Cobol developers

Copilot's usefulness on Cobol is real but inconsistent: it has seen far less Cobol in training
than Java or C#, so expect more hallucinated syntax, more suggestions that don't compile, and
weaker performance on anything involving copybooks or JCL specifics. Treat every suggestion as
a first draft to verify against your compiler and your own knowledge of the codebase, more so
than you would for Java or .NET code. It's still worth trying. Just calibrate your trust
accordingly.

## If you get stuck

See `../../ai-tooling-fallback.md` for what to do if Copilot itself isn't working (license
issues, rate limits, or access problems), or ask your coach.
