# Session Prompts
# File: .kit/00-prompts.md

Copy and paste the prompt that matches what you are doing.

Wherever you see `[domain]` in a prompt, replace it with the name of your vocabulary folder:
`software` | `writing` | `production` | `manufacturing`

Wherever you see `[description]`, `[filename]`, or `[milestone]` — fill in the actual value before sending.
For how the session files work: `.playbook/guides/context-kit.md`

---

## Starting a New Project

**Message 1** — upload `01-process.md` + `03-execution.md`, then send:

```
I'm starting a new project using Playbook. I'll share files in two messages.
This message: Process + Execution. Next: Exploration + Progress.

Based on my project — [1–2 sentence description] — help me populate these files:
- Draft initial open questions for Exploration
- Suggest guidelines for Execution
- Set the Current State and initial goals

Just give me a brief confirmation for now.
```

**Message 2** — upload `02-exploration.md` + `04-progress.md`, then send:

```
Here are the remaining blank templates. Just a brief confirmation.
```

---

## Picking Up Where You Left Off

**Message 1** — upload current `01-process.md` + `03-execution.md`, then send:

```
I'm resuming a project using Playbook. I'll share files in two messages.
This message: Process + Execution. Next: Exploration + Progress.

Focus only on what is current. Do not reopen settled decisions unless I ask.
Just a brief confirmation.
```

**Message 2** — upload current `02-exploration.md` + `04-progress.md`, then send:

```
Here is the context from earlier work. Just a brief confirmation.
[Optionally describe what you want to work on this session.]
```

---

## End of Session

```
Based on this session, update my kit files:
- Check off completed tasks in 03-execution.md
- Move settled decisions to 02-exploration.md with dates and reasoning
- Append to 04-progress.md — Completed, Decided, Fixed, In Progress
- Update Current State in 01-process.md

Generate updated markdown for all changed files.
```

---

## When the AI Loses the Thread

```
Stop. Your only source of truth is the files uploaded at the start of this session.
Ignore everything else in the conversation.
Re-read the uploaded files before continuing.
```

---

## Starting an AI Work Session

Upload `AI-INSTRUCTIONS.md` + `vocabulary-[domain]/AI-INSTRUCTIONS.md`, then send:

```
Before any work, read in this order:
1. AI-INSTRUCTIONS.md
2. vocabulary-[domain]/AI-INSTRUCTIONS.md
3. docs/flows/_index.md
4. docs/contracts/_index.md
5. .playbook/_index.md

[Attach relevant planning docs for the current task]

Confirm your understanding. Then wait for my instruction.
```

---

## Picking Up from a Milestone Summary

Upload `docs/rollout/[milestone].md` + `AI-INSTRUCTIONS.md` + `vocabulary-[domain]/AI-INSTRUCTIONS.md`, then send:

```
I'm resuming work on an existing project.
Read the milestone summary first, then the two instruction files.
Confirm your understanding before we begin.
```

---

## Writing Paths and Rules

Attach `docs/[brief or PRD].md`, then send:

```
Given this brief, define the paths and rules for this project.

Paths (docs/flows/):
- Write docs/flows/_index.md as the entry point
- Write docs/contracts/global.md first — rules that apply everywhere
- Write one path file per major flow
- Include what happens when things go wrong, not just the good path
- Keep files small — one path per file

Rules (docs/contracts/):
- Write docs/contracts/_index.md as the entry point
- Write one rules file per path
- Required sections: The Good Path, Ways It Can Fail, Edge Cases,
  Ordering Rules, Hard Constraints
- Mark N/A if a section does not apply. Mark TODO if it is not yet written.

Flag anything unclear. Wait for review before writing any planning documents.
```

---

## Building the Planning Structure

Attach the brief + approved path and rules files, then send:

```
Given this brief and the approved paths and rules, build the planning structure.

- Create an _index.md for the project and each section with a living map
- Create planning documents for all identifiable pieces, coordinators,
  concerns, and integrations — mark all as status: draft
- For coordinators, include a sequence section referencing the relevant path files
- Flag anything unclear
```

---

## Adding a New Piece

Attach the parent `_index.md` + relevant path file, then send:

```
Given this parent document and path, create a planning document
for [description of the piece].
Flag any missing connections.
After creating it, give me the updated Structure section for the parent _index.md.
```

---

## Sync Check

```
Run a sync check on all planning documents touched this session.
For each: does the current work match the planning document?
List anything that has drifted. Update affected documents.
Update the folder map in any _index.md where pieces changed.
```

---

## Generating a Milestone Summary

Attach all `.playbook/` docs + `.kit/04-progress.md`, then send:

```
Generate a milestone summary for [milestone name].
Read the full planning structure and progress log.
Output to docs/rollout/[milestone].md.
```

---

## Bringing an Existing Project In

Upload `AI-INSTRUCTIONS.md` + `vocabulary-[domain]/AI-INSTRUCTIONS.md`, then send:

```
I am bringing an existing project into Playbook.
Read the two instruction files first. Confirm you have read them.
Do not begin any work yet.
Then follow .playbook/guides/adopt.md.
```

---

## Audit Expansion — Start Inventory

Upload instruction files + all available project context, then send:

```
The audit expansion is active. Current phase: inventory.

Map and document:
1. All major pieces, sections, and components and what they do
2. All outside tools or dependencies and what they provide
3. The information structure — what exists and how it is organised
4. Entry points — how the project is accessed or consumed
5. What depends on what

Produce a draft docs/rewrite/system-map.md. Use plain language.
Flag anything unclear. Wait for review.
```

---

## Audit Expansion — Move to Next Phase

```
Audit expansion current phase: [inventory | audit | classify | risk-map | gap-report | handoff]
Previous phase reviewed and approved.

Move to [next phase].
Follow .playbook/guides/reverse-engineer.md.
Wait for review before moving again.
```

---

## Audit Expansion — Handoff

Attach all `docs/rewrite/` files + current kit files, then send:

```
The audit expansion is complete. Run the handoff.

1. Pull open questions from system-map.md → .kit/02-exploration.md
2. Pull settled decisions from decision-record.md → .kit/02-exploration.md
3. Pull known problems from risk-register.md → .kit/03-execution.md
4. Update .kit/01-process.md — mark audit complete, update Current State

Generate updated kit files for review.
```

---

## Video Insights — Extract with Gemini

Send to Gemini (not Claude) with the video URL:

```
Please summarise the main arguments in '[Video Title]' by [Channel Name].
Use the transcript to highlight the most important takeaways.
[URL]

Structure your response using this template:

---
title: "[Video Title]"
source: "[Channel Name]"
url: [URL]
date: [Today's Date]
type: [general | tutorial | case-study | framework | opinion | research]
topics: [2–4 relevant tags]
relevance:
---

## Core Idea
One paragraph. The central argument or insight.

## Key Points
3–5 most important takeaways. No more.

## Connections
Leave blank.

## Status
Pending

Output as a downloadable markdown file.
```

Add one extra section based on the video type — see `guides/video-insights.md`.

Save the output to: `.kit/_refs/video-insights/[short-descriptive-title].md`

---

## Video Insights — Discuss in a Session

Upload the insight file alongside your kit files, then send:

```
I have a new video insight to discuss.
File: .kit/_refs/video-insights/[filename].md

Review it and:
1. Summarise the core idea in one sentence
2. Identify connections to our current project
3. Flag any conflicts with decisions already made
4. Suggest whether this opens a question, settles a decision,
   adds a task, or flags a risk
5. Ask me how I want to proceed

Do not integrate anything yet. Surface the connections and wait.
```

---

## Video Insights — Integrate

After the discussion, if integrating:

```
Integrate [insight title] as follows:
- [Open question | Settled decision | Task | Known problem]
- Ref: .kit/_refs/video-insights/[filename].md

Update the insight file Status to: Integrated
Log in 04-progress.md: Decided: [title] — Integrated
```

---

## Approaching the Session Limit

```
We are approaching the session limit.
Update all kit files and any changed planning documents now.
Generate the markdown for each so I can save them before we continue.
```
