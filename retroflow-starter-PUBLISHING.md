# Publishing the RetroFlow starter repo

**Facilitator-only: do not put this file, or anything else outside `retroflow-starter/`,
into the repo you publish to participants.**

`retroflow-starter/` (the sibling folder to this file) is the complete source for the
developer day's starter project, the "half-built backend from a previous vendor" participants
fork and clone. It needs to be published as its **own, separate, public GitHub repository**,
distinct from this internal `ai-track-ps` facilitator materials repo.

## Why it can't just be a folder participants clone from this repo

This repo (`ai-track-ps`) contains facilitator-only content participants must never see before
(or during) the day: `block4-facilitator-notes.md` with the full trap writeups and answers,
the trap-tracking checklists, the QA seeded-bugs table, the FA day's engagement-manager
briefing. Cloning or forking anything that shares git history with this repo risks exposing
that, even if participants are only told to look at one folder. The starter project needs a
clean home with no relationship to this repo at all.

## One-time setup

1. **Create a new, empty GitHub repository**: something like `stackcraft/retroflow` or
   `cegeka-ai-track/retroflow-starter`, whichever naming convention fits where this will live.
   Public or org-internal, whichever matches how participants will access it (a private org
   repo is simplest if everyone has the same GitHub org access already).

2. **Push `retroflow-starter/`'s contents as a fresh repo**, with its own clean history:
   don't carry over `ai-track-ps`'s commit history:

   ```bash
   cd module-2/developer/retroflow-starter
   git init
   git add .
   git commit -m "Initial RetroFlow starter: half-built backend from previous vendor"
   git branch -M main
   git remote add origin <your-new-repo-url>
   git push -u origin main
   ```

3. **Verify it from a clean clone**, the way a participant will experience it:

   ```bash
   cd /tmp && git clone <your-new-repo-url> retroflow-verify
   cd retroflow-verify
   mvn compile
   ```

   `mvn compile` should succeed (main sources only). `mvn test` is expected to **fail to
   compile** at this stage: the inherited test file references `TeamService` and
   `RetrospectiveService`, which don't exist yet on purpose. Building those is Block 1's job.
   This matches the brief's own framing: *"The codebase compiles. That is about all that can
   be said for it."*

   > **Note on this verification:** I built and structurally checked every file in this
   > sandbox (balanced braces, package declarations matching directory paths, well-formed
   > `pom.xml`) but could not run a live `mvn compile` myself, this sandbox's network policy
   > blocks Maven Central, the same class of restriction the Laptop Requirements doc already
   > flags for corporate networks. Please run the verification above once, for real, after
   > publishing; it's a two-minute check and it's the one thing I couldn't confirm directly.

4. **Update the materials checklist** in `developer/facilitator-guide.md` and
   `Module2-Laptop-Requirements.md` with the real repo URL once it exists (currently they just
   say "GitHub" / "Shared link before the day").

## Keeping it in sync later

If the RetroFlow brief or business rules ever change, edit `retroflow-starter/` here first
(it's the source of truth, version-controlled alongside everything else), then re-push the
updated contents to the published repo. The test-engineer day's reference implementation
(`module-2/test-engineer/`, not yet built) will need to be built from whatever this starter
evolves into, so keeping this one accurate matters beyond just the developer day.
