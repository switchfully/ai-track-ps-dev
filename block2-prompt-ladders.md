# Block 2 Prompt Ladders
**11:15 — 12:45 · Pair AC and Pair BD work in parallel**

Pairs have rotated. You are now working with someone you haven't paired with today yet.
Before you write a single prompt: spend 5 minutes reading the code your teammates wrote
in Block 1. You will be building on it — and possibly fixing it.

---

## Pair AC — Business logic & service layer tests

Your job: implement the service layer that enforces all the business rules,
then write tests that prove those rules actually work.

---

### Before you start — read the code

Open the service stubs and the three failing tests in `RetroflowBusinessRulesTest`.
Do not run the tests yet. Just read them.

Ask yourself: based on what Block 1 produced, do the method signatures the tests expect
actually exist? If not, that's your first task — not a prompt task, a reading task.

---

### Prompt 1 — Implement the service layer

```
I have a Spring Boot REST API called RetroFlow. Here are the business rules I need to enforce:

1. A team must have at least one member
2. A team can only have one OPEN retrospective at a time — creating a second should throw an exception
3. A retrospective can be closed but never reopened
4. Feedback items cannot be added to, modified in, or deleted from a CLOSED retrospective
5. An action item (a subtype of feedback item) can be marked as completed, but once completed
   it cannot be uncompleted
6. Action items must be filterable by priority (LOW/MEDIUM/HIGH) and by completed status

Here are my entity classes: [paste entities]
Here are my repository interfaces: [paste repositories]
Here are my service stub method signatures: [paste service stubs]

Implement the full service layer. Business rules should be enforced in the service layer,
not in the controller or entity. Throw specific exceptions for each rule violation.
Use constructor injection, not field injection.
```

**What to do with the output:**
- Go rule by rule: find where each rule is enforced in the generated code
- Check rule 4 specifically — "cannot be added to, modified in, or deleted from"
  That's three operations. Is all three covered, or just one?
- Check rule 5: is `uncompleteActionItem` explicitly throwing an exception, or does it
  silently do nothing? Silent failure is worse than a loud failure.

---

### Prompt 2 — Make the failing tests pass

Run the three tests: `mvn test -Dtest=RetroflowBusinessRulesTest`

They might still fail (the service methods now exist but may not match the test's expectations exactly).

```
I have three failing tests. Here are the test file and the error output:

[paste test file]
[paste mvn test output]

The test logic is correct and must not be changed. Fix the service layer so all three
tests pass. Explain what was wrong with each failure.
```

**What to do with the output:**
- Read the explanation — don't just apply the fix
- Each failure should teach you something about the gap between "AI implemented it" and
  "it actually does what the test expects"
- Run the tests again: `mvn test -Dtest=RetroflowBusinessRulesTest`
  All three should now be green.

---

### Prompt 3 — Expand test coverage

```
The three existing tests cover the core business rules. Now help me expand coverage.

For each of the following, write a JUnit 5 + AssertJ test using @SpringBootTest:

1. Creating a team with no members should fail with a 400-equivalent exception
2. Retrieving a team that does not exist should throw an appropriate exception
3. Adding a feedback item to an OPEN retrospective should succeed and return the saved item
4. Closing a retrospective that is already closed should throw an exception
   (or: should it be idempotent? Make a decision and implement it consistently)
5. Filtering action items by priority should return only items with that priority
6. Filtering action items by completed=true should return only completed items

For each test, use the Arrange-Act-Assert pattern with clear comments.
```

**What to do with the output:**
- Test 4 is interesting — Copilot will make a choice. Is it the right choice?
  Check your `decisions.md` from Block 1. Is this consistent?
- Run all tests: `mvn test`
- Fix anything that fails before moving on

---

### Prompt 4 — Feedback item update and delete

```
I need to implement the update and delete operations for feedback items.
Business rule: these operations must fail if the feedback item belongs to a CLOSED retrospective.

Current service code: [paste service]
Current test suite: [paste test file]

1. Implement updateFeedbackItem(Long id, String newContent) in the service
2. Implement deleteFeedbackItem(Long id) in the service
3. Write tests for both operations: one test for the happy path, one test that verifies
   the closed-retrospective rule is enforced

Make sure the tests are added to the existing test class, not a new file.
```

---

## Pair BD — Copilot agent mode: codebase refactor

Your job: use Copilot in agent mode to refactor the entire RetroFlow codebase
in a single coordinated operation. You may not edit individual files manually.
Everything goes through the agent.
---

### Before you start — understand what you're doing

Copilot agent mode (available in VS Code via the Copilot Chat panel, selecting "Agent" mode)
can read and edit multiple files in a single operation. It is not magic — it operates on the
files you have in your workspace, makes a plan, and executes changes step by step.

> **No IDE installed, or no paid Copilot license?** Open the RetroFlow starter repo in a free
> **GitHub Codespace** instead (Code → Codespaces → Create codespace on main, on the repo's
> GitHub page) — it's a full VS Code running in your browser, nothing to install, with Java 21
> and Maven already configured. Enable **GitHub Copilot Free** (no credit card, free.github.com
> or the Copilot extension's sign-in prompt) — it includes agent mode, capped at 50 requests a
> month, which comfortably covers one block. Everything below works exactly the same once
> you're in.



Your job: give it a good enough instruction that the changes are correct and consistent.
Your constraint: **do not touch any file manually**. If the agent makes a mistake, fix it
with another agent prompt, not by editing directly. This is how you learn where agents fail.

---

### The refactor brief

The RetroFlow codebase has the following problems (many of these will be genuine issues
from what Block 1 produced):

1. **Naming inconsistencies** — some methods use `get` prefix, some don't; some use `Id` suffix, some use `id`
2. **Missing JavaDoc** — public service methods and controller endpoints have no documentation
3. **Methods that are too long** — any method over 20 lines should be broken down
4. **Missing `@Transactional` annotations** — service methods that write to the DB should be transactional
5. **Inconsistent exception messages** — error messages should follow the pattern: `"[EntityType] [id]: [reason]"`

---

### Prompt 1 — Plan before acting

Before letting the agent touch any files:

```
I have a Spring Boot codebase that needs refactoring. Before making any changes,
analyze the codebase and produce a refactoring plan.

For each of these problems, list every file and method that needs to change:
1. Naming inconsistencies (get prefix, Id/id suffix)
2. Missing JavaDoc on public methods
3. Methods over 20 lines that should be broken down
4. Missing @Transactional on service write methods
5. Inconsistent exception messages (should follow: "[EntityType] [id]: [reason]")

Do not make any changes yet. Just list what needs to change and why.
```

**What to do with the output:**
- Read the plan carefully. Does it match what you can see in the code?
- Are there things it missed? Things it flagged that aren't actually problems?
- Only proceed to Prompt 2 once you agree with the plan.

---

### Prompt 2 — Execute the refactor

```
Now execute the refactoring plan. Apply all five categories of changes across the entire codebase.

Constraints:
- Do not change any method signatures that are referenced in the test files
- Do not change the package structure
- Do not add new dependencies to pom.xml
- After each category of changes, pause and list what you changed before moving to the next

Start with the @Transactional annotations, then naming, then JavaDoc,
then method extraction, then exception messages.
```

**What to do with the output:**
- Watch what the agent does between categories — this is the observable "reasoning" of an agent
- If it tries to change a method signature that a test depends on: stop it and tell it why
- Keep a running note: what did the agent change that you didn't expect?

---

### Prompt 3 — Verify nothing broke

```
The refactoring is complete. Now verify the codebase is still correct:

1. Are there any compilation errors?
2. Do any method names now mismatch their usages (controller calling a service method
   that was renamed)?
3. Are the test files still valid — do they reference methods that still exist with
   the same signatures?
4. Is there any @Transactional missing on write operations?

List every issue you find. Do not fix anything yet — just list.
```

Then, after reviewing the list:

```
Fix all the issues you identified. Apply the fixes now.
```

---

### Prompt 4 — Reflect on the agent

This is not a Copilot prompt. This is a discussion prompt for your pair.

Talk through these questions and write your answers in `decisions.md`:

1. What did the agent do that surprised you?
2. At what point, if any, did you not trust what it was doing?
3. If you were doing this on a production codebase at a client, what guardrails would you want?
4. What would have happened if you had let it run without checking the plan first?

---

## End of Block 2 checklist

Before lunch at 12:45, your team should be able to answer yes to:

**Pair AC:**
- [ ] All three original failing tests now pass
- [ ] At least 6 additional tests exist and pass
- [ ] `mvn test` runs clean (no test failures)
- [ ] Update and delete for feedback items are implemented and tested

**Pair BD:**
- [ ] Agent refactor completed — all five categories addressed
- [ ] `mvn test` still runs clean after the refactor
- [ ] `decisions.md` updated with agent reflection answers

**Both pairs together:**
- [ ] Code merged and `mvn verify` runs clean
- [ ] You have something to say at Retro 2 about what the agent did unexpectedly
