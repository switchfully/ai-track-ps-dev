# Block 2: Working with an agent
Pair AC and Pair BD work in parallel.

New pairs! This block, both pairs work with an AI agent. An agent is an AI that reads and edits your files by itself, runs commands like `mvn test`, reads the output and goes again. That changes your job quite a bit: you're no longer writing prompts that produce code, you're giving instructions, reviewing a plan and checking what it changed.

> There's one rule for this block: you don't edit any file by hand. The only exception is `prompt-log.md`. If the agent gets something wrong, you fix it by giving it another instruction. It will feel slower at times, but this is exactly how you find out where agents go wrong.

**This block, you write the prompts yourself.** This morning you got them ready-made. Now we only tell you the goal, what a good prompt for it needs to contain, and what to check afterwards. How you word it is up to you.

> Keep a **prompt log** in `prompt-log.md` in the root of the repo. Every time you write a prompt, paste it in and add one line: did it work, or did you have to fix it, and what did you change? This is the start of your team's prompt library, and you'll use it again in Block 3.

> Stuck after two tries? Ask your coach for the example prompt for that step. Looking is allowed, just write down what the example does that yours didn't.

> **Watch your tokens.** An agent burns through requests much faster than a chat: it reads files, runs the build, reads the output and tries again. So name the files in your prompt, and start a **new chat** for each step (keep the plan in your prompt log, not in the chat). Your tool says you're out of requests? Don't wait for it to reset: work on your partner's machine or switch tools, and tell your coach.

**Where things live.** Course files (cheat sheets, standards): https://github.com/switchfully/ai-track-ps-dev. The code and every file you create: your group's fork of https://github.com/switchfully/ai-track-ps-dev-retroflow.

**How to read this block.** Every step starts with a **Read:** line (the files it works with, these exist already) and a **Create:** line (the file you make or change, and where it lives). The **Goal** is your *done when*: if the Goal is true, you move on. Not sure what to do now? Look at those lines first, then ask your coach.

**Your block at a glance.** This is a rough guide, not a rule.

| Part | About |
|---|---|
| Setting up | 10 min |
| Warm-up | 5 min |
| Step 1: plan | 10 min |
| Step 2 | 15 min |
| Step 3 | 15 min |
| Step 4 | A stretch step. Start it only when step 3 is done and you have 15 minutes left |
| Bring it together | The last 15 minutes. Don't skip this one, Block 3 starts from the merged `main` |

---

## Setting up (both pairs, about 10 minutes)

* **Read:** your group's repo, as it is on `main`
* **Create:** your own branch and `prompt-log.md` in the root of the repo
* **Done when:** you are on your branch, the chat is in Agent mode, and Source Control shows a clean working tree

* Pull the latest `main` of your group's repo. It has everything from Block 1, including `decisions.md`, `test-matrix.md` and `.github/copilot-instructions.md`.
* Open the repo in your IDE, or in a GitHub Codespace if your local setup is a problem (Code → Codespaces → Create codespace on main). Open the Copilot chat panel and switch the chat from **Ask** to **Agent**. Using Claude Code? Start it in the root of the repo. It reads `CLAUDE.md` instead of the Copilot instructions file, so ask it to create that from your instructions file.
* Create your own branch, so the two pairs don't step on each other's files: pair AC works on `block2-service`, pair BD on `block2-web`.
* Create `prompt-log.md`.
* Open the **Source Control** panel on the left (or run `git diff` in a terminal). Every file the agent touched is listed there, and when you click one, you see exactly what changed. You'll be using this view all block long, because this is how you check what an agent did.

You each own a part of the code. Stay inside it, that's what makes the merge at the end painless.

| | Pair AC | Pair BD |
|---|---|---|
| Works on | the service layer, entities, repositories | the controllers, request/response objects, error handling |
| Tests live in | `src/test/.../service` | `src/test/.../controller` |

### Warm-up: your first agent task

* **Read:** the test classes and the `pom.xml`
* **Create:** nothing. No file may change

**Goal:** the agent runs the build itself and explains in at most 10 lines why `mvn test` fails right now. No files may change.

A good prompt for this:
* Tells the agent to run the command itself
* Says in so many words that nothing may be changed
* Asks for what is missing, per failing test

Then check it:
* Does the explanation match what you saw at the end of Block 1?
* Did the agent run the command, or did it just read the code and guess?

---

## Pair AC: make the rules real, then attack your tests

Your job: get the service layer working with the agent running the tests, generate a full test suite from the matrix you made this morning, and then find out whether those tests would actually notice a bug.

### Step 1: plan before you act

Never let an agent start changing files before you've seen its plan. That's the habit of this block.

* **Read:** `RetroflowBusinessRulesTest`, `test-matrix.md`, `decisions.md`, the service classes and the brief
* **Create:** nothing in the code. Paste the plan in `prompt-log.md`, you need it in step 2

**Goal:** a plan, and no changed files yet, for three things:
1. Making the three tests in `RetroflowBusinessRulesTest` compile and pass: which service methods are missing, or have a different signature than the test calls
2. Enforcing every rule from `test-matrix.md` in the service layer, and which of those rows already have a test
3. Where each rule gets enforced, and which exception it throws

A good prompt for this:
* Tells the agent which files to read first
* Says in so many words that nothing may be changed yet
* Asks for a plan per part, not one long list
* Asks for the file and the method behind every planned change
* Says what to do when the matrix and the brief disagree (hint: flag it, don't decide it)

After you got the plan:
* Read it before you let it do anything. The closed-retrospective rule is three operations in the matrix. Are all three in the plan?
* Does it enforce the rules in the service layer, and not in the controller?
* Does the plan change a test? It shouldn't. Tell the agent.

### Step 2: let the agent run the tests

* **Read:** your plan, and `RetroflowBusinessRulesTest` (read only, you never edit it)
* **Create:** changes in the service layer, entities and repositories under `src/main`. No test changes

**Goal:** the three vendor tests are green, and the agent got there by running `mvn test` itself, as many times as it needed.

A good prompt for this:
* Points to the plan, so you don't have to repeat it
* Tells the agent to run the tests itself after each part and keep going until they pass
* Says the agent has to stop after each part and list what it changed
* Sets hard limits on the tests: `RetroflowBusinessRulesTest` is never edited, never skipped, never disabled. If a test looks wrong, the agent stops and tells you
* Also sets limits on the rest: no new dependencies, nothing gets removed without asking you

After each part, open the changed files in the Source Control panel.
* Is `RetroflowBusinessRulesTest` untouched? If it's changed, ask the agent why, and put it back.
* What does `uncomplete` do on a completed action item? A loud exception is right, silently doing nothing is not.
* Look at how the agent reacted when a test failed. Did it fix the code, or did it try to get around the test?

### Step 3: generate the test suite

* **Read:** `test-matrix.md` and your instructions file
* **Create:** a new test class in `src/test/.../service`, next to the vendor's test

**Goal:** one test for every row in `test-matrix.md`, all green, in a new test class next to the vendor's test.

A good prompt for this:
* Points to the matrix and says: one test per row
* Says how tests have to be named and built (your instructions file might already say it)
* Wants assertions on real values and messages, not just "the result is not null"
* Tells the agent to run them and report the result
* Says what to do with a row it can't test: flag it, don't skip it silently

Then check it:
* Count the tests. Is it the same number as the rows in the matrix?
* Take three tests and read them. Does the assertion check what the row says should happen?
* A new test that is green the first time proves very little. Ask the agent to show you that the test fails when the rule is broken.

### Step 4: attack your own tests

Green tests only mean that nothing they check is broken. They don't tell you how much they check. **Mutation testing** answers that question: a tool makes small changes to your code (flip a condition, remove a check, change a return value) and runs your tests after each change. A test suite that doesn't fail after a change has just missed a bug.

* **Read:** `pom.xml`, the service classes and your test classes
* **Create:** the PIT setup in `pom.xml` and extra tests in the test class from step 3. The PIT report is generated in `target/`, you don't write it yourself

**Goal:** a list of bugs your tests don't notice, and new tests that do.

A good prompt for this:
* Asks the agent to add a mutation testing tool for Maven (PIT) and to check that the plugin version it picks really exists before it uses it
* Limits the run to the service classes and your test classes, otherwise it takes forever
* Wants the surviving mutants listed: file, line, and what was changed
* Wants new tests that kill them, without changing any production code
* Asks for the mutation score before and after
* Says what to do if PIT won't run after two tries: sabotage the code by hand instead, one bug at a time, run the tests, put the code back

Then check it:
* Pick one survivor. Read the changed line. Would you have spotted this bug in a code review?
* Look at the delete path of a feedback item in a closed retrospective. Did a mutant survive there?
* Did the score really go up? Ask the agent to show you the report.

---

## Pair BD: pin down the API, then restructure it

Your job: find out what the API does today with tests the agent writes, then restructure the web layer with the agent while those tests keep you honest. The services are being built by pair AC right now, so you test the web layer with the services mocked.

### Step 1: plan before you act

* **Read:** the controllers, the brief and `decisions.md`
* **Create:** nothing in the code. Paste the plan in `prompt-log.md`. The status code you decide on goes in `decisions.md`

**Goal:** a plan, and no changed files yet, for three things:
1. Which endpoints exist, and what each of them returns today (status code and body), compared to the brief and `decisions.md`
2. Contract tests for every endpoint: success, invalid input (400), not found (404) and a business rule violation
3. A restructuring: DTOs instead of entities in requests and responses, one global exception handler with one error format, validation on the request objects and no logic in the controllers

A good prompt for this:
* Tells the agent which files to read first
* Says in so many words that nothing may be changed yet
* Asks for a plan per part, and for the file and class behind every planned change
* Says what to do when the brief doesn't give an answer (hint: flag it, don't decide it)

After you got the plan:
* The brief doesn't say which status code a business rule violation should return. Which one did the agent pick, and why? Decide it yourselves and write it in `decisions.md` (through the agent, of course).
* Does the plan touch services or entities? Those belong to pair AC. Tell the agent to stay out.

### Step 2: write the contract tests first

* **Read:** your plan and the controllers
* **Create:** a test class per controller in `src/test/.../controller`

**Goal:** a web-layer test class for every controller, all endpoints covered. Some of them will fail. That's the point: those are real problems in the code.

A good prompt for this:
* Points to the plan and says which controllers to cover
* Tells the agent to test only the web layer, with the services mocked, so you don't depend on pair AC
* Wants the tests to work with JSON in and JSON out, and not with the Java classes behind it. That way they still work after the restructuring
* Wants the status code and the body checked, not just the status
* Tells the agent to run them and report which ones fail and why, but not to fix anything yet

Then check it:
* Take a failing test. Is the code wrong, or is the test wrong? Walk through it with your partner before you let the agent change anything.
* Did the agent write a test that can never fail?

### Step 3: restructure while the tests watch

* **Read:** your plan, the failing contract tests and the controllers
* **Create:** changes in the web layer only: controllers, new request and response classes, and one `@RestControllerAdvice`. Services, entities and repositories are off limits

**Goal:** all contract tests are green, there is no entity in any request or response, there is one `@RestControllerAdvice` and one error format.

A good prompt for this:
* Points to the plan and to the failing tests
* Gives the order of work and says to stop after each part and list what changed
* Tells the agent to run the contract tests after every part
* Says the tests only change when the agent asks you first. Since they work with JSON, they shouldn't need to
* Protects pair AC's work: no changes in services, entities or repositories
* Doesn't allow removing anything without asking you

After each part:
* Open the Source Control panel. Did a test change? If so, why?
* Ask the agent: "Show me every place where an entity is still returned or accepted by a controller." Do you agree with the list?
* Do all errors have the same shape now, including validation errors?

### Step 4: lock it in with an architecture test

A restructuring that nobody enforces will be undone in two months. You can turn the rules into tests with **ArchUnit**, a library that checks the structure of your code.

* **Read:** the web layer as it is after step 3, and `pom.xml`
* **Create:** the ArchUnit dependency in `pom.xml` and one architecture test class in `src/test`

**Goal:** a test class with at least three architecture rules, for example: controllers don't use repositories, controllers don't return entities, services don't depend on controllers. Plus proof that the rules can actually fail.

A good prompt for this:
* Asks the agent to add ArchUnit as a test dependency, and to check that the artifact and version really exist before adding them
* Names the rules (or asks for the three most useful ones, with a reason)
* Wants proof: break each rule on purpose, show that the test fails, then put the code back

Then check it:
* Open the `pom.xml` yourself and look at the dependency. Did you check it exists? You'll need that habit on every project from now on.
* Is the code really back to how it was after the experiment? Check the Source Control panel.

---

## Finished early? Try a second tool

Take a step you just did and run the same task in another agent, for example Claude Code (`ai-cheat-sheets/claude-code-cli.md`). Compare the diffs. What did the other agent change that yours didn't? Share your conclusion in Retro 2.

---

## The last 15 minutes, both pairs: bring it together

* **Read:** the branches `block2-service` and `block2-web`
* **Create:** the merged `main`
* **Done when:** `mvn verify` runs clean on `main` and you know your test count

* Push your branches. Merge `block2-service` and `block2-web` into `main`. Conflicts? Let the agent help you resolve them, and read what it did.
* Run `mvn verify` on the merged code. Fix what's broken, with the agent.
* Count: how many tests do you have now, and how many are green?
* Pick your **best** and your **worst** prompt from your log. You'll be asked for them in Retro 2.
* Think of one thing the agent did that you didn't expect. You'll tell it in Retro 2.

---

## Done?

This is what we look at in Retro 2. Your coach asks one pair to show their result on the shared screen, so be ready to open the diff and the report.

Pair AC:
* The three vendor tests are green, and `RetroflowBusinessRulesTest` is unchanged
* There is a test for every row in `test-matrix.md`
* You know the mutation score before and after, and you can name a bug your first tests missed

Pair BD:
* Contract tests exist for every endpoint, and they are green after the restructuring
* No controller returns or accepts an entity, and all errors have the same shape
* The architecture rules are tests, and you saw them fail once

Both pairs:
* The branches are merged and `mvn verify` runs clean
* Your prompt log has every prompt you wrote, each with a one-line verdict
* You picked your best and your worst prompt

What you learned in this block:
* What the difference is between chatting with AI and letting an agent work on your code
* Why you always let an agent make a plan before it starts changing things
* What happens when an agent can run the tests itself, and why the tests have to be off limits
* How to check what an agent really did, by reading the diff instead of its summary
* Why green tests don't mean good tests, and how to find out
* What a prompt needs to contain to keep an agent on a leash
