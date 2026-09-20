# Claude Code: A Terminal-Based Alternative

Claude Code is a command-line tool: instead of living inside your IDE as a chat panel, it runs
in your terminal and works directly on your project's files. Not everyone has access to it
(it requires a paid Claude plan), but if you do, it's worth knowing how it differs from Copilot
and when you might reach for it instead.

## How it's different from Copilot

Copilot lives where you're already looking (your editor). Claude Code lives in your terminal
and treats your whole project as its workspace from the start. In practice this means:

- You describe a task in plain language directly in the terminal, and it plans, edits files,
  runs commands (build, test, lint), reads the output, and iterates, largely on its own.
- It tends to work well for tasks that span many files or require running things repeatedly to
  check progress (for example: "get this failing test suite passing").
- You review its proposed changes as diffs before they're applied, similar to Copilot's agent
  mode, just from the command line instead of an editor panel.

Neither tool is strictly better. Copilot's tighter IDE integration is convenient for quick,
in-context edits. Claude Code's terminal-native, project-wide approach can be more effective for
larger, multi-step tasks. Today's exercises are built around Copilot, but if you already have
Claude Code available, feel free to use it for the same exercises and compare the experience.

## Getting started

1. Install it (requires Node.js): `npm install -g @anthropic-ai/claude-code`
2. Run `claude` from your project's root directory in a terminal.
3. Log in when prompted (requires a Claude Pro plan or higher).
4. Type your task in plain language, for example:
   > "Add a Retroflow business rule that flags a complaint as overdue after 5 working days
   > unless an extension has been approved. Add a test for it."
5. Claude Code will propose a plan, then start making changes. Review each file diff it shows
   you before it proceeds, and answer any clarifying questions it asks.

Useful things to know once you're in a session:

- Type `/help` to see available commands.
- You can interrupt at any point and redirect it with a follow-up instruction.
- It remembers context within a session, so you can have a back-and-forth the same way you
  would in a chat.

## When to reach for it

- A task that touches many files and needs verification at each step (run the tests, check the
  result, fix what's broken, repeat).
- When you want the AI working directly against your terminal tools (build, test runner, git)
  rather than through an IDE's abstraction of them.

## When Copilot is probably simpler

- A quick, single-file change while you're already looking at the code.
- Anything where you want to stay in your editor rather than switch to a terminal.

## If you don't have access

Claude Code requires a paid plan and isn't available to everyone. This is fine, it's a bonus
tool for today, not a requirement. See `../../ai-tooling-fallback.md` for the fallback options
if you're relying on agent mode and don't have Copilot or Claude Code available.
