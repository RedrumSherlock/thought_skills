---
name: first-principles
description: Analyze a statement, question, or design from first principles. Define the real goal, keep only proven facts, reason or build step by step from those facts, then have an independent sub-agent judge the work.
disable-model-invocation: true
---

# First Principles

Strip the ask down to proven facts, then rebuild from those facts alone. Do not reason by analogy ("others do it this way") or by authority ("best practice says").

Input: $ARGUMENTS

Run the four steps in order. Do not skip a step.

## Step 1: Define the real goal

People often ask about the method they have in mind (X) when what they need is a goal (Y). This is the XY problem. Find Y.

1. Restate the ask in one sentence.
2. Ask "why?" until you reach the outcome itself, not a way to get it. What is the job to be done? If the current system did not exist, what would actually be needed?
3. Ask if the requirement should exist at all. Try deleting it: what happens if we just don't do this? The worst mistake is to optimize something that should not exist.
4. Write success criteria: how we will know the goal is met.
5. List the must-haves the user stated. Keep them; do not swap in your own.

**Gate:** if any part of the goal is a guess, ask the user and wait. Do not go on until the goal is clear.

## Step 2: Collect only facts

List every claim the ask rests on, stated or hidden. Most asks carry several hidden ones. A claim is a fact only if it has one of these kinds of evidence:

- **Logic, math, definitions, or laws of physics.**
- **Direct observation:** something measured, run, or read in the real code, data, or logs.
- **Published science:** a peer-reviewed study. Cite the original paper, not a summary of it.
- **Official numbers:** recent figures from an authoritative body, such as a national statistics office or a standards body.
- **The user's confirmed goal and must-haves** from Step 1.

These are **not** evidence: blog posts, forums, "best practice", "industry standard", "everyone uses X", what other companies do, market prices taken as a cost floor, and numbers from memory.

For published or official numbers, read the original source and check that it is not misrepresented. The date, unit, scope (what was measured, where, on whom), and definitions must match how we use it. Old numbers are snapshots, not current facts. Quote the source.

Test each fact: Is it proven, not just commonly believed? Would breaking it really cause failure? Stop breaking a fact down once it is proven; going deeper adds nothing.

| ID | Fact | Evidence (source, date) |
|----|------|-------------------------|
| F1 | ...  | ...                     |

| Unproven claim | Why not proven | What would prove it |
|----------------|----------------|---------------------|

Only F-items may be used in Step 3. Never fill a gap with an invented number. If a missing fact would change the result, say so and name how to get it.

## Step 3: Reason or build from the facts

**If the ask is a question, build a reasoning chain:**

```
C1: F1 + F3 → [conclusion]   because [why it follows]
C2: C1 + F2 → [conclusion]   because [why it follows]
Answer: C2 + F4 → [answer]
```

- Each step may use only F-items and earlier C-items.
- If a step needs something not on the list, stop and go back to Step 2. Do not slip it in.
- If a step is likely but not certain, say so.

**If the ask is to build a solution, start from the bare minimum:**

1. Start with the simplest thing that meets the goal. Example: for a network service, a plain Linux machine running one process.
2. Add one layer at a time (framework, database, cache, queue...). Each layer must name the fact that forces it.
3. For each layer, ask: does it earn its complexity? Remove it. If the solution still meets the goal, leave it out.
4. When two options both meet the goal, pick the one with fewer moving parts.
5. Stop as soon as every success criterion from Step 1 is met.

| Layer | Needed because (F#) | If removed |
|-------|---------------------|------------|

**For both:** compare the result to the usual answer. Say what differs and which fact causes it. If the usual answer turns out right, say so; do not rebuild just to be different. End with the cheapest test that could prove the result wrong.

## Step 4: Independent judge

Spawn a fresh sub-agent as the judge. Give it only the goal and success criteria, the fact table, the unproven list, and the Step 3 chain or layer table with its result. Do not give it the chat, your notes, or your opinion. Send it this prompt with those items:

```
You are an independent judge. Check this first-principles analysis for mistakes.
You have not seen how it was made. Trust nothing; check everything.
You may look up sources to verify them.

1. Facts: Is each one really proven by its evidence? Is the source original,
   authoritative, and current? Is any number misquoted (date, unit, scope)?
   Is any "fact" really best practice, convention, or opinion?
2. Reasoning or build: Does each step follow only from the listed facts and
   earlier steps? Flag any hidden assumption, analogy, or "everyone does it".
   For a build: is any layer not forced by a fact?
3. Gaps: Was any fact, constraint, or option ignored that would change the result?
4. Result: Does it meet the goal and success criteria?

Return each issue as: [item ID] problem → fix.
End with PASS or FAIL.

[analysis]
```

Fix every issue, then run a new judge. Stop when it returns PASS or after two rounds. Report any issue still open.

## Final output

```
## First Principles Analysis: [topic]
**Goal:** ...
**Success criteria:** ...
**Facts:** [F table]
**Unproven:** [table]
**Reasoning / Build:** [chain or layer table]
**Result:** ...
**Differs from the usual answer because:** ...
**Test that could prove it wrong:** ...
**Judge:** PASS, or the open issues
```
