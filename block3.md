# Block 3: Grounding and skills

Last rotation of the day! This block, both pairs connect their AI to a real source document: StackCraft's coding standards. We serve that document through an MCP server we host ourselves. Once that works, both pairs take what they learned today and turn it into a skill: a set of instructions the AI only loads when it actually needs them.

What we want you to notice is the difference between an AI that answers from what it was trained on ("this is what good Spring Boot code usually looks like") and an AI that reads StackCraft's actual document and quotes it back to you.

**This block, you write all the prompts yourself.** No hints about wording this time. For every task we only tell you what the result has to look like, and how you check it. You've had a whole day of practice, so now it's up to you.

> Keep adding to your **prompt log** (`prompt-log.md`): the prompt, and one line on whether it worked or what you had to fix. Your best prompts are the raw material for the skill you build at the end of this block.

> Stuck after two tries? Ask your coach for the example prompt for that step. Looking is allowed, just write down what the example does that yours didn't.

> **Watch your tokens.** Start a **new chat** for each step and name the files in your prompt. Your tool says you're out of requests? Don't wait for it to reset: work on your partner's machine or switch tools, and tell your coach.

---

## Setting up (both pairs, about 10 minutes)

* Pull `main` of your group's repo. It has the merged work of both pairs from Block 2.
* Create your own branch: `block3-audit` for pair AD, `block3-review` for pair BC.
* Connect your AI tool to the MCP server. The server is called `ai-track-standards`, the URL is `https://ai-track-ps-production.up.railway.app/mcp` and there's no login. Pick the option that fits your tool:

**VS Code or a Codespace (GitHub Copilot):** create a file `.vscode/mcp.json` with the following content, and click the small **Start** link that appears above the server name.

```json
{
  "servers": {
    "ai-track-standards": {
      "type": "http",
      "url": "https://ai-track-ps-production.up.railway.app/mcp"
    }
  }
}
```

**IntelliJ or another JetBrains IDE:** Settings → Tools → GitHub Copilot → Model Context Protocol (MCP) → **Configure**. This opens the same `mcp.json` format, so add the block above and save.

**Claude Code:** run `claude mcp add --transport http ai-track-standards https://ai-track-ps-production.up.railway.app/mcp` in your terminal.

* Open the chat in Agent mode and take a look at the tools list. You should see `list_documents` and `read_document` in there.

> Curious about what you're actually connecting to? `ai-cheat-sheets/what-is-mcp.md` explains it.

**What you need to know to talk to the server:** it has two tools (`list_documents` and `read_document`), and the document we need has the id `stackcraft-coding`. An AI only reads that document when you tell it to, it won't do it by itself.

---

## Both pairs, step 1: feel the difference

You're going to ask the same question twice: first with the `ai-track-standards` server switched off in your tool's tool list, then with it switched on. For this one, we give you the question, so both runs are comparable:

```
What exact format does StackCraft require for exception messages in the service layer,
and which HTTP status code do we have to use when a business rule is violated?
```

For the second run, you write the extra sentence that makes the AI use the server and quote the document. Add it at the end of the question.

* Without the connector, the AI has never seen StackCraft's standards. Did it tell you that, or did it come up with something that sounds plausible?
* With the connector, open `stackcraft-coding-standards.md` and look up the section it quoted. Does it exist? Is it quoted correctly?
* Now look at your own code. What does the exception message format look like in your service layer? You'll need that answer in a minute.

---

## Pair AD: auditing the codebase against the standards

### Step 2: the audit

**Your mandate:** find out where RetroFlow violates StackCraft's coding standards.

The result is good when:
* Every finding names the file and the line
* Every finding quotes the section of the standards it violates
* Every finding explains why it matters
* Every finding comes with the correct implementation
* The findings are sorted by severity, Critical first, using the severity in the standards document

Then check it:
* Take three findings and look up the quoted section in the document. Can't find it? Then the AI made it up. Write it down, it's great material for Retro 3.
* Take two findings and open the file at the line it names. Is the violation really there?
* Did the AI find the feature-first package structure rule (§1.1)? Think about why that one is tricky: the starter project was layer-first on purpose.

### Step 3: fix the Critical findings, with the tests watching

**Your mandate:** fix every Critical finding, one at a time, and keep the build green.

The result is good when:
* Every fix is a separate step, and `mvn test` runs after each one
* Nothing in the existing tests was changed to make a fix pass
* Every fix has a short note: what was wrong and which section it's based on
* If a fix needs a new test, it has one

Then check it:
* Open the Source Control panel. Did a test change? If so, why?
* Run `mvn verify` yourself. Does it still pass?

---

## Pair BC: reviewing the code before it goes to staging

You're the senior developer who reviews RetroFlow before it goes to staging. The AI will find real issues, but it will also invent a few that aren't there. It's up to you to tell them apart.

### Step 2: the review, with proof

**Your mandate:** review the codebase on four dimensions: security, API design, business rules and test quality. An agent can do more than read code. It can start the application and call it.

The result is good when:
* Every finding has proof: a line of code it quotes, or a command it ran with the output (a failing request, a test result)
* Every finding has a severity (Critical, High, Medium, Low) and a recommended fix
* The business rules from the brief are checked one by one, as three separate operations where the brief says "modified or deleted"
* Nothing is changed in the code. This is a review, not a fix

Then check it:
* The brief says no authentication is required. If the AI flags missing authentication anyway, note it: AI reviewers don't always respect the scope you gave them.
* Look at the SQL and JPQL injection findings. Is there really string concatenation in a query, or is it flagging parameterised queries? That's a common false positive.
* Pick two Critical or High findings and reproduce them yourself. Is it real?
* Look for tests that pass without proving anything, like a lone `isNotNull()`. Did the AI find some?

### Step 3: the review document

**Your mandate:** combine the findings into one document you could send to the development team, and save it as `review/pre-staging-review.md`.

The result is good when it has:
* A summary of 3 or 4 sentences
* The critical issues (must fix before staging)
* The other issues (should fix, and fix when time allows)
* What's done well (at least 3 things)
* A sign-off status: BLOCKED / CONDITIONAL / APPROVED

Then check it:
* Do you agree with the status it gave? You'll have to defend it during the showcase.
* Can you name one finding that turned out to be a false positive, and one that was real?

---

## Both pairs, the last 25 minutes: build a skill

By now you've written a lot of instructions for the same kind of check. A skill saves you that work. It's a folder with a `SKILL.md` file in it, which tells the AI how to do one specific task. The AI only reads the short description of each skill it has, and loads the full instructions when your request matches that description.

> How is this different from the instructions file you made this morning? That file is read before every request. A skill is only read when it's relevant. `ai-cheat-sheets/working-efficiently.md` explains it in a bit more detail.

Pair AD builds `stackcraft-audit`, a skill that audits any Java project against StackCraft's standards. Pair BC builds `stackcraft-tests`, a skill that writes and reviews tests the StackCraft way.

### Step 4: draft the skill

A `SKILL.md` file always looks like this. That's the format, the AI doesn't know it by heart:

```
---
name: the-name-in-lowercase-with-hyphens
description: One or two sentences. What the skill does and when to use it.
---
The instructions: the steps to follow, what to check, what sources to use, the exact
output format, and what never to do.
```

**Your mandate:** have the AI write the `SKILL.md` for the task you just did, based on the prompts that worked best for you today (your prompt log is useful here).

The result is good when:
* It follows the format above
* It's short: under 40 lines
* The instructions are specific enough that someone else could follow them without asking you anything
* It tells the AI where the source of truth is, so it reads the standards through the MCP server instead of using what it remembers

Then read what it wrote and edit it yourselves. Spend most of your time on the description: that's what the AI uses to decide whether to load your skill or not.

> **Level up:** a skill can be more than one file. Put a template next to `SKILL.md` (a test class that shows the exact naming and layout, for example) and refer to it from the instructions. Or add a small script that does a mechanical check, like a `grep` for field injection, and let the skill run it.

### Install it and swap

* Using GitHub Copilot in VS Code or a Codespace? Ask the agent to save it as `.github/skills/<skill-name>/SKILL.md`. Copilot picks it up by itself, and you can also start it with `/<skill-name>` in the chat. Not showing up? Check that agent skills are switched on in the VS Code settings, and tell your coach.
* Using Claude Code? Save it as `.claude/skills/<skill-name>/SKILL.md`.
* Now swap with the other pair, and test their skill in a new chat, without explaining what it does:
  * Pair AD runs `stackcraft-tests` to write the tests for the Critical fixes they made in Step 3.
  * Pair BC runs `stackcraft-audit` on the codebase, and compares the result with their own review. What did the skill find that the review missed? And the other way around?
* Did the AI pick up the skill on its own? Did it do what the other pair had in mind? Give them one piece of feedback.

---

## Finished early? Build your own MCP server

Everything so far connected to a server your coach already built and hosted. This is the other side of that.

**Your mandate:** build a minimal local MCP server that makes your own `decisions.md` readable for an AI tool, and connect your tool to it.

The result is good when:
* It uses the official MCP SDK for Node.js, with the stdio transport (this is for local use, one client at a time)
* It has two tools, `list_documents` and `read_document`, like the hosted server
* You registered it in your tool the same way you registered the hosted one

Then check it:
* Ask your AI a question that only your `decisions.md` can answer. Once with the server on, once with it off. Compare.
* Discuss with your pair: when would you run your own MCP server at a client, and when would you connect to one someone else hosts?

---

## Done?

Pair AD:
* You have an audit with quoted sections, and you checked at least three of those quotes
* The Critical findings are fixed, one by one, without touching an existing test
* `stackcraft-audit` exists and BC tested it

Pair BC:
* You have a review where every finding has proof, and `review/pre-staging-review.md` is committed
* You can name a false positive and a real finding, and you can defend your sign-off status
* `stackcraft-tests` exists and AD tested it

Both pairs:
* You saw the difference between an answer with and without the connector
* `mvn verify` still passes on your branch
* Your prompt log is complete, and you know which of your prompts you'd reuse on a real project
* You each have one concrete thing to share at Retro 3

What you learned in this block:
* What grounding is, and why an answer from the real document beats an answer from training data
* What MCP is and how it connects an AI tool to a source
* How to check whether a quoted standard actually exists
* Why an agent that can run things gives you review findings with proof, and why you still reproduce the important ones
* What a skill is, how it differs from instructions, and why the description matters so much
* How to brief an AI when all you have is the result you want
