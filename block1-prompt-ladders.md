# Block 1 Prompt Ladders
**09:30-11:00 · Pair AB and Pair CD work in parallel**

These are structured prompts, not free-form suggestions. Work through them in order.
The goal of this block is not to finish as much as possible: it is to build prompting habits
that will make the rest of the day faster and safer.

After each prompt: read the output critically before using it. If something feels off, it probably is.

---

## Pair AB: Domain model & REST API skeleton

You are building the entity model and the basic CRUD endpoints for RetroFlow.

---

### Prompt 1: Understand before you build

Open Copilot Chat. Paste the full contents of the RetroFlow brief and send this prompt:

```
I'm joining a project to finish a half-built Spring Boot backend called RetroFlow.
Here is the project brief:

[paste brief here]

Explain the domain to me as if I'm a new developer joining the team.
Then identify every ambiguity or missing detail you find in the requirements.
Be specific: don't just say "the requirements could be clearer."
```

**What to do with the output:**
- Go through every ambiguity Copilot identified. Are they real? Did it miss any?
- Discuss with your pair: how would you resolve each one?
- Write down your decisions in a file called `decisions.md` in the root of the repo.
  These are your team's interpretation of the spec. You'll refer back to them.

> **Watch for:** Copilot may confidently resolve ambiguities for you without flagging them.
> That's a risk. Make sure the decisions are yours, not the AI's defaults.

---

### Prompt 2: Entity model proposal

```
Based on this domain brief, propose a complete JPA entity model for RetroFlow in Java 21
with Spring Boot 3.x. Include:
- All fields with appropriate types and JPA annotations
- Relationships between entities with correct cardinality and fetch strategies
- Validation annotations where appropriate
- The FeedbackItem/ActionItem inheritance using an appropriate JPA strategy

For each design decision, explain why you made it: especially where you had a choice
between alternatives (e.g. inheritance strategy, fetch type, collection type).
```

**What to do with the output:**
- Compare the proposed entity model to your `decisions.md`: does it match your decisions?
- Check the inheritance strategy Copilot chose. Do you agree? What are the trade-offs of
  the alternative?
- Check the fetch strategies. Are lazy relationships going to cause N+1 problems anywhere?
- Do not copy-paste blindly. Adapt the output to match your team's decisions.

---

### Prompt 3: Implement the entities

Once you've agreed on the model:

```
Implement the following entities for RetroFlow as complete Java classes:
Team, Retrospective, FeedbackItem, ActionItem.

Requirements:
- Java 21 (you can use records for DTOs when appropriate, but entities should be classes)
- Spring Boot 3 / Jakarta EE annotations (not javax)
- Include all-args constructor, no-args constructor, getters and setters
- Do not use Lombok, write explicit code
- Follow this inheritance strategy: [insert your chosen strategy]
- Apply these validation constraints: [summarise your decisions]

Provide each class in full. No placeholders or TODOs.
```

**What to do with the output:**
- Replace the stub entities in the repo with the generated code
- Make sure the package declarations are correct (`com.stackcraft.retroflow.entity`)
- Verify the application still compiles: `mvn compile`

---

### Prompt 4: REST endpoints for Teams and Retrospectives

```
Implement Spring Boot REST controllers for the following operations in RetroFlow.
Use a service layer: do not put business logic in the controller.

Operations:
- POST /api/teams: create a team (name + list of member names)
- GET /api/teams/{id}: get a team by id
- POST /api/teams/{teamId}/retrospectives: create a retrospective for a team
- GET /api/teams/{teamId}/retrospectives: list all retrospectives for a team
- PUT /api/retrospectives/{id}/close: close a retrospective

For each endpoint:
- Return the correct HTTP status code for success and for common error cases
- Validate all input: reject invalid requests with 400 and a meaningful message
- Throw an appropriate exception for business rule violations (do not return nulls)

Also generate the corresponding service class stubs with method signatures but no
implementation yet: we will implement the business logic separately.
```

**What to do with the output:**
- Check the HTTP status codes. Are they correct? (201 for creation, 404 for not found, etc.)
- Check the error handling: is it using @RestControllerAdvice or something else?
- Are the service method signatures sensible?

---

### Prompt 5: Interrogate the output

This is the most important prompt of the block. Do not skip it.

```
Review the REST API you just generated for RetroFlow against the following business rules
from the brief. For each rule, tell me:
1. Whether it is currently enforced in the code
2. Where exactly it is (or should be) enforced
3. What would happen if a client tried to violate it right now

Business rules to check:
- A team can only have one OPEN retrospective at a time
- A closed retrospective cannot be reopened
- Feedback items in a closed retrospective cannot be modified or deleted
- Completed action items cannot be uncompleted
```

**What to do with the output:**
- This is your quality gate. Anything missing needs to be added before the block ends.
- Note down which rules Copilot says are enforced but you cannot verify in the code, those
  need a test before you trust them.

---

## Pair CD: Project scaffolding & CI pipeline

You are setting up the project infrastructure: a clean build, a working Dockerfile,
and a CI pipeline that keeps the team honest.

---

### Prompt 1: Audit the existing setup

```
I have a Spring Boot 3.x Maven project. Here is the pom.xml:

[paste pom.xml contents]

And here is the application.properties:

[paste application.properties contents]

The README says this project uses Gradle and PostgreSQL. The actual files suggest otherwise.
Audit this setup and tell me:
1. What is actually configured?
2. What the README got wrong?
3. Anything missing or misconfigured for a production-ready REST API?
4. What would you add or change?
```

**What to do with the output:**
- Fix the README first: it should reflect reality
- Note the improvement suggestions. Which ones are worth doing today?

---

### Prompt 2: Dockerfile

```
Write a production-quality Dockerfile for this Spring Boot Maven application.

Requirements:
- Multi-stage build (build stage + runtime stage)
- Minimize final image size: use a JRE, not a JDK, in the runtime stage
- Do not run the application as root
- The build stage should run `mvn package -DskipTests`
- The final image should expose port 8080
- Include a HEALTHCHECK instruction

After the Dockerfile, explain each instruction and why you made each choice.
Specifically address: why multi-stage? what are the security implications of running as root?
```

**What to do with the output:**
- Read the explanation, not just the Dockerfile
- Check: does the non-root user setup actually work? (Common mistake: creating the user but
  not assigning file ownership correctly)
- Save the Dockerfile to the repo root

---

### Prompt 3: GitHub Actions CI pipeline

```
Write a GitHub Actions workflow for this Spring Boot Maven project.

The workflow should:
- Trigger on push to any branch and on pull requests to main
- Run on ubuntu-latest with Java 21 (use actions/setup-java with temurin distribution)
- Cache Maven dependencies to speed up builds
- Run `mvn verify` (compile + test)
- Fail the build if test coverage drops below 80% (use JaCoCo Maven plugin)
- On pull requests, post a comment with the test coverage percentage

Also add the JaCoCo plugin configuration to the pom.xml so coverage reporting works.

After generating the workflow, identify the top 3 security risks in CI/CD pipelines
and tell me whether this workflow has any of them.
```

**What to do with the output:**
- This is a good moment to actually think about the security question, not just copy the answer
- Check the JaCoCo config: does it actually fail the build on low coverage, or just report?
  (These are different configurations: Copilot often generates reporting without enforcement)
- Save the workflow to `.github/workflows/ci.yml`

---

### Prompt 4: Interrogate the pipeline

```
Review this GitHub Actions workflow for common misconfigurations:

[paste the generated workflow]

Specifically check for:
1. Pinned vs unpinned action versions (security implications)
2. Whether secrets are handled correctly
3. Whether the workflow could be manipulated by a malicious pull request
   (look up "GitHub Actions pwn requests" if you're not familiar)
4. Any permissions that are broader than necessary

For each issue you find, suggest a fix.
```

**What to do with the output:**
- This prompt deliberately asks Copilot to audit something Copilot just generated
- See if it catches its own mistakes; it often does, which is a useful thing to demonstrate
- Apply at least the critical fixes before the block ends

---

### Prompt 5: Developer experience

```
What else should this repository have for a developer joining the project for the first time?
Think about: local development setup, IDE configuration, how to run tests, how to test the API
manually, any tooling that would make the development loop faster.

Generate a proper README.md that replaces the existing (incorrect) one.
Include a "Getting Started" section that gets someone from a fresh clone to a running API
in under 5 minutes.
```

**What to do with the output:**
- A good README is a real deliverable, don't treat this as a throwaway task
- Check that the commands in the README actually work before committing it

---

## End of Block 1 checklist

Before Retro 1 at 11:00, your team should be able to answer yes to:

**Pair AB:**
- [ ] `decisions.md` exists with your spec interpretation documented
- [ ] Entity stubs replaced with complete implementations
- [ ] Application compiles (`mvn compile` passes)
- [ ] Controller and service stubs exist for Teams and Retrospectives
- [ ] You've run Prompt 5 and know which business rules are not yet enforced

**Pair CD:**
- [ ] README.md reflects reality
- [ ] Dockerfile exists and builds
- [ ] `.github/workflows/ci.yml` exists with JaCoCo coverage gate
- [ ] You can articulate the security concern from Prompt 4

**Both pairs together:**
- [ ] Code from both pairs is committed and merged (deal with any conflicts now)
- [ ] `mvn verify` runs without build errors (tests will fail, that's expected)
