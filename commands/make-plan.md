---
description: Research, grill, and write a plan
---

# Make Plan

Turn an objective into a plan worth approving. Research first, settle the open decisions with the user, then write something short enough to read in one pass.

Arguments: $ARGUMENTS — the objective. Prefix with `quick` to skip the grilling round.

Think as deeply as the objective warrants — a one-file change needs far less than one crossing a module boundary.

## 1. Frame

**Call `EnterPlanMode` first**, unless already in plan mode. Nothing in this command edits a file — the mode is what guarantees it.

Then read `$ARGUMENTS`. If its first whitespace-separated token is exactly `quick`, set **quick mode** and drop that token; `quickly refactor the parser` is an objective, not a flag. Restate what remains in one line.

## 2. Search project knowledge

Search available memory/knowledge tools using keywords from the objective and the modules it touches. Look for prior decisions, known gotchas, conventions that constrain the approach, and past attempts at the same problem. A plan that contradicts a settled decision is worse than no plan.

## 3. Research

Read the actual code before proposing anything. Trace the real flow end to end, through every file the change touches.

**Facts are your job, decisions are the user's.** Never ask what you can look up. Dispatch sub-agents for broad searches; ask the user only what the codebase can't answer.

If the code contradicts the objective's premise, say so now, before Step 4. A well-built plan for the wrong problem is the expensive failure here.

## 4. Grill

Skip entirely in **quick mode**.

Otherwise call the Skill tool with `grilling` and follow it — the design tree, the frontier, the rounds — with one override on **how** questions reach the user:

- **Discrete choice → `AskUserQuestion`.** Your recommendation is option 1, its label suffixed `(Recommended)`. Cap is 4 questions per call: a wider frontier takes a second call, never a trimmed frontier.
- **Genuinely open → prose**, in the skill's own format. A question with no enumerable answers isn't improved by inventing three.

Batch the frontier. One question at a time is the slow path the rounds exist to avoid.

`AskUserQuestion` is normally reserved for what you can't settle from sensible defaults. That bar doesn't apply here — the user ran this command to be asked. A decision you *could* default is still theirs to make.

## 5. Write the plan

**Concise by construction.** The per-section caps below are the budget; there's no global word count to game. A rename is a three-line plan, a twenty-file refactor is twenty one-line steps. A plan may run long because it has many steps — never because a section grew.

**Readable.** Write for someone deciding in under a minute. Plain sentences, no throat-clearing, no paragraph whose real job is to look thorough. If a line could be cut without changing the decision, cut it.

**Shape:**

- **Why** — two sentences. The symptom, goal, or constraint. Don't restate the objective back at the user.
- **Steps** — numbered, one line each, naming the file and what changes in it. No paragraph under a step arguing the step is a good idea. Group them under phase headings **only** when something happens between the groups — a review, a deploy, a separate PR, a checkpoint where you'd stop and verify before continuing. Work that runs straight through is one ungrouped list, however long.
- **Verify** — how the user confirms it worked. Bullets, at most four, each a check plus its expected result. One line saying so if there's nothing to check manually.
- **Risks** — only when a step can fail in a way the user would want to hear about first. Omit the heading otherwise.

**No Context section.** Not under that name or any other. What you learned while researching is not part of the plan — the two-sentence Why carries anything that changes a decision, and the rest belongs in the conversation or a memory file. This is the single biggest source of bloat.

Cut every sentence defending a decision already settled with the user — they were there. A plan is what you'll do, not the case for doing it.

## 6. Carry the constraints

End every plan with this block verbatim, plus anything the grilling settled:

```
Constraints:
- Comments explain why, never what. No comment that restates the line below it.
- Doc comments on public declarations only.
- Match the file's existing comment density.
```

Emit it even when the project or user instructions already say the same — where they do, those govern in full. Often they won't. These travel *with* the plan so they're re-read at implementation time: honor them while building, not just while writing.

## 7. Present

Call `ExitPlanMode` with the plan. Don't edit anything before it's approved.
