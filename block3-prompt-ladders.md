# Block 3 Prompt Ladders
**13:45 — 15:15 · Pair AD and Pair BC work in parallel**

Final pair rotation. This block goes deeper than anything you've done today.
The goal is not to finish everything — it's to experience what these tools actually feel like
and where they genuinely change the way you work.

---

## Pair AD — MCP server: grounded AI with real context

You are going to connect to a hosted MCP server and use it to give Copilot
real, grounded context from StackCraft's internal documentation.
This is the foundation of how serious AI-assisted development works —
not relying on the model's training data, but feeding it the actual source of truth.

---

### What is MCP and why does it matter?

MCP (Model Context Protocol) is a standard that lets AI tools connect to external data sources
and tools as first-class capabilities. Instead of copy-pasting context into a chat window,
you give the AI a live connection to the information it needs.

Today you'll connect to a hosted MCP server that serves StackCraft's actual coding standards
document, then use it to audit the RetroFlow codebase. When you ask Copilot a question, it
can call that server to read the real document and ground its answer in it, rather than
making things up from training data.

---

### Setup (do this before prompting)

No local install either way — this is a hosted server, not a local one. The steps differ
slightly by IDE:

**VS Code (1.101+):** Open `.vscode/mcp.json` in your project (or run **MCP: Add Server** from
the Command Palette) and add:
```json
{
  "servers": {
    "ai-track-standards": {
      "type": "http",
      "url": "<hosted MCP server URL — ask your coach>"
    }
  }
}
```

**IntelliJ IDEA (or other JetBrains IDEs):** Settings → Tools → GitHub Copilot → Model Context
Protocol (MCP) → **Configure**. This opens the same `mcp.json` format — add the block above and
save. Copilot detects the new server automatically.

**Visual Studio:** Add an `mcp.json` file to your solution (Solution Explorer → right-click →
Add → New Item, or add it at the solution root by hand) with the same content as above — Visual
Studio also auto-detects a `.vscode/mcp.json` if one already exists in the folder, so if you're
switching between VS Code and Visual Studio on the same checkout you don't need to duplicate it.

Verify (any IDE): open Copilot Chat in agent mode. You should see `ai-track-standards` listed as
an available tool, exposing `list_documents` and `read_document`.

---

### Prompt 1 — Verify the connection

```
Using the ai-track-standards MCP server, call read_document with id "stackcraft-coding" to
read the StackCraft coding standards document, and give me a summary of the top 5 rules that
are most likely to be violated in a Spring Boot REST API built under time pressure.
```

**What to do with the output:**
- If Copilot reads from the actual file: the MCP connection works. The answer will reference
  specific rules from the StackCraft standards.
- If Copilot makes up generic Java advice: the MCP connection is not working.
  Check the setup steps before continuing.

The difference matters. This is the difference between grounded AI and hallucinated AI.

---

### Prompt 2 — Audit the codebase against the standards

```
Using the ai-track-standards MCP server's read_document tool (id "stackcraft-coding") to
read the StackCraft coding standards, audit the RetroFlow codebase for violations.

For each violation:
- Name the file and line number
- Quote the relevant standard that is violated
- Explain why it's a violation
- Suggest the correct implementation

Sort violations by severity: Critical → Major → Minor.
```

**What to do with the output:**
- Check: are the quoted standards actually in the StackCraft document, or did Copilot invent them?
  This is easy to verify — open the document and check.
- This is a live demonstration of grounded vs. ungrounded output.
  Note down any invented standard references for Retro 3.

---

### Prompt 3 — Fix the critical violations

```
From the audit, take all Critical violations and fix them in the codebase now.
For each fix:
- Apply the change
- Add a comment explaining what was wrong and what was fixed
- If a test should cover this, write the test
```

---

### Prompt 4 — Expand the use of MCP

Now that you've seen MCP working with a hosted server, think about what else this
could connect to. This is a discussion + research prompt:

```
What other MCP servers are publicly available that would be useful for a Java developer
working on a Spring Boot API project at a client site?

Search for and list 5 real, available MCP servers (not hypothetical ones) with:
- What they connect to
- What you could do with them in a development workflow
- Any security considerations for using them at a client
```

**What to do with the output:**
- Verify the listed servers actually exist, don't take Copilot's word for it
- Note: this is a good test of hallucination risk. MCP servers are new enough that the model's
  training data may be incomplete or outdated.
- Write your findings in `decisions.md` under a "MCP exploration" section

---

### Optional stretch: build your own local MCP server

**Only attempt this if you've finished Prompts 1 through 4 with time to spare.** It is not
part of the End of Block 3 checklist, and there's no expectation that every pair gets here.

Everything so far connected to a server your coach already built and hosted. This is the
other side of that: building the server yourself, so you understand what's actually happening
underneath the connection, not just what it feels like to use one.

```
I want to build a minimal local MCP server in Node.js that exposes exactly one text file on
my machine as a document an AI tool can read. Use the official MCP TypeScript/JavaScript SDK
with the stdio transport (not HTTP, this is for local use only, one client at a time).

It should expose two tools: list_documents (returns the name of the one file), and
read_document (returns its contents given that name). Point it at my decisions.md file.

Give me the complete server code, plus the exact steps to register it in VS Code
(.vscode/mcp.json, "type": "stdio", "command" pointing at node and the script path).
```

**What to do with the output:**
- Run it, then verify the same way you verified the hosted server in Prompt 1: ask Copilot a
  question that only your `decisions.md` would answer correctly, with the server connected
  and without it, and compare.
- If you want a second example, point it at a different file instead of duplicating the
  server, one document is enough to prove the mechanism works.
- Discuss with your pair: now that you've seen both sides, what would make you choose to run
  your own MCP server at a client versus connecting to one someone else hosts? Add your
  answer to `decisions.md` under "MCP exploration."

---

## Pair BC — AI-assisted code review: structured quality gate

Your job: use AI to produce a structured review of the entire RetroFlow codebase —
the kind of review a senior developer would do before a production deployment.
This teaches you to use AI as a reviewer, not just a generator.

The interesting challenge: AI could find real issues AND invent fake ones.
Your job is to tell the difference.

---

### The review mandate

You are acting as StackCraft's senior developer. Before RetroFlow goes to staging,
you need to produce:

1. A **security review** — what could go wrong if this API was exposed to the internet?
2. An **API design review** — is this a well-designed REST API?
3. A **business rules review** — are all the rules from the brief actually enforced?
4. A **test quality review** — do the tests actually test the right things?

---

### Prompt 1 — Security review

```
Perform a security review of this Spring Boot REST API codebase.

[paste all controller and service code]

Check for:
1. Missing authorization (any endpoint that should restrict access but doesn't)
2. Input validation gaps (what could a malicious client send?)
3. SQL injection or JPQL injection risks
4. Sensitive data exposure in API responses or error messages
5. Any other security concern you identify

For each finding:
- Severity: Critical / High / Medium / Low
- File and approximate line
- Description of the vulnerability
- A concrete example of how it could be exploited
- Recommended fix
```

**What to do with the output:**
- The brief explicitly says no auth is required — so "missing authentication" is not a valid finding
  for today's scope. If Copilot flags it anyway, note this: AI reviewers don't always respect scope.
- Check the JPQL/SQL injection findings carefully. Is there actual string concatenation in
  any repository queries? Or is Copilot flagging parameterised queries as risky?
  This is a common false positive.
- For each Critical or High finding: verify it in the code before accepting it.

---

### Prompt 2 — API design review

```
Review this REST API design for correctness and consistency.

[paste controller code and endpoint list]

Evaluate:
1. Are the HTTP methods correct for each operation? (GET should not modify state, etc.)
2. Are the status codes correct? (201 for creation, 404 for not found, 409 for conflicts, etc.)
3. Are the URL patterns consistent and RESTful?
4. Are error responses consistent in structure?
5. Is there anything that would be surprising or non-obvious to an API consumer?

Produce a review in this format:
- PASS / FAIL / CONCERN for each category
- For each FAIL or CONCERN: specific finding + recommended fix
```

**What to do with the output:**
- The `PUT /api/retrospectives/{id}/close` endpoint is worth discussing:
  should a "close" operation be a PUT? Or a PATCH? Or a POST to a sub-resource?
  There's no single right answer — but the review should surface the question.
- Check the 409 Conflict usage: is it being used for the "duplicate open retrospective" case?
  If not, what status code is being returned instead?

---

### Prompt 3 — Business rules review

```
Here is the original project brief with all business rules:

[paste the business rules section of the brief]

Here is the service layer implementation:

[paste service code]

For each business rule in the brief:
1. State whether it is enforced in the service layer
2. If yes: quote the exact code that enforces it
3. If no: state that it is missing and what would happen without it
4. If partially: describe what's missing

Be exhaustive. Check every single rule.
```

**What to do with the output:**
- This is the most important review of the day. Cross-reference every finding against the actual code.
- Pay special attention to: "Feedback items in a CLOSED retrospective cannot be MODIFIED OR DELETED"
  — that's two operations. Is both covered?
- If Copilot says a rule is enforced but you can't find it in the code: ask it to quote the
  specific line. If it can't, it was hallucinating.

---

### Prompt 4 — Test quality review

```
Here are the tests for RetroFlow:

[paste all test code]

Review the test suite for:
1. Tests that pass but don't actually test the right thing (wrong assertions)
2. Business rules from the brief that have no test coverage at all
3. Tests that are too broad or too narrow
4. Any test that could give a false sense of security

For each finding, show me the specific test and explain the problem.
If there are tests with incorrect assertions, show me what the assertion should be.
```

**What to do with the output:**
- This is adversarial by design. Copilot is reviewing tests that may themselves have been
  generated by Copilot. Can it catch its own mistakes?
- Note: a test that uses `assertThat(result).isNotNull()` when it should check the actual
  value is a very common AI-generated test smell. Look for it.
- Fix any test quality issues it identifies — these are real problems.

---

### Prompt 5 — Compile the review document

```
Based on the four reviews we've done (security, API design, business rules, test quality),
produce a single structured review document I could realistically send to the development team.

Format:
# RetroFlow Pre-Staging Review
## Executive Summary (3-4 sentences)
## Critical Issues (must fix before staging)
## Major Issues (should fix before staging)
## Minor Issues / Recommendations (fix when time allows)
## What's done well (at least 3 things)
## Sign-off status: BLOCKED / CONDITIONAL / APPROVED

Be honest. If there are real problems, flag them as blocking.
```

**What to do with the output:**
- Save this as `review/pre-staging-review.md` in the repo
- This is your showcase artifact for Retro 3
- Note the sign-off status — and whether you agree with it

---

## End of Block 3 checklist

Before Retro 3 at 15:15:

**Pair AD:**
- [ ] MCP server is running and verified (Prompt 1 output references real file content)
- [ ] Audit completed and critical violations fixed
- [ ] `decisions.md` updated with MCP exploration findings
- [ ] You can explain the difference between grounded and ungrounded AI output

**Pair BC:**
- [ ] All four reviews completed
- [ ] `review/pre-staging-review.md` committed to the repo
- [ ] You can name at least one finding that was a false positive and one that was real
- [ ] Sign-off status determined and you can defend it

**Both pairs together:**
- [ ] Code merged and `mvn verify` still passes
- [ ] You each have one concrete thing to share at Retro 3
