# Playbook -- Session Prompts

# File: .kit/00-prompts.md

Quick reference for every session type. Copy and paste as needed.

For how the Context Kit works within Playbook:
.playbook/.guides/context-kit.md

---

## Context Kit -- Standalone Prompts

These work with any AI tool, any project type.

### Fresh Project

Message 1
Upload .kit/01-process.md + .kit/03-execution.md, then send:

I'm starting a new project using the Context Kit.
I'll share files in two messages.

This message: Process + Execution
Next message: Exploration + Progress

Based on my project [1-2 sentence description], help me populate these files:

- Draft initial Exploration questions
- Suggest Execution guidelines
- Define Current State and initial goals

Just provide a brief confirmation.

Message 2
Upload .kit/02-exploration.md + .kit/04-progress.md, then send:

Here are the remaining blank templates. Just provide a brief confirmation.

---

### Resume Project

Message 1
Upload current .kit/01-process.md + .kit/03-execution.md, then send:

I'm resuming a project using the Context Kit.
I'll share files in two messages.

This message: Process + Execution
Next message: Exploration + Progress

Focus only on Current State. Do not reopen closed decisions unless I ask.
Just provide a brief confirmation.

Message 2
Upload current .kit/02-exploration.md + .kit/04-progress.md, then send:

Here is the context from earlier work. Just provide a brief confirmation.
[Optionally describe what you want to work on this session.]

---

### End of Session

Based on this session, update my kit files:

- Check off completed tasks in 03-execution.md
- Move closed decisions to 02-exploration.md with dates and reasoning
- Append to 04-progress.md -- Completed, Decided, Fixed, In Progress
- Update Current State in 01-process.md

Generate updated markdown for all changed files.

---

### Context Rot Recovery

Stop. Your only source of truth is the files uploaded at session start.
Ignore all prior conversation context.
Re-read the uploaded files before continuing.

---

## Playbook Pipeline Prompts

These extend the kit for Playbook-specific work.

### Start Dev Session

Upload .playbook/00-rules.md + docs/code-standards.md, then send:

Before any work, read in this order:

1. .playbook/00-rules.md
2. docs/code-standards.md
3. docs/flows/\_index.md
4. docs/contracts/\_index.md
5. .playbook/\_index.md

[Attach relevant module and component docs]

Confirm your understanding. Then wait for my instruction.

---

### Resume from Rollout Doc

Upload docs/rollout/[milestone].md + .playbook/00-rules.md + docs/code-standards.md, then send:

I'm resuming work on an existing project.
Read the rollout doc first, then 00-rules.md and code-standards.md.
Confirm your understanding before we begin.

---

### Write Flows and Contracts

Attach docs/PRD.md, then send:

Given this PRD, define the flows and contracts for this project.

Flows (docs/flows/):

- Write docs/flows/\_index.md as the entry point and dependency map
- Write docs/contracts/global.md first -- architecture, auth, error handling rules
- Write one flow file per major runtime path
- Declare scope and trigger type in frontmatter for each flow
- Include all failure paths -- not just the happy path
- Keep files small -- one flow per file; split at ~150 lines

Contracts (docs/contracts/):

- Write docs/contracts/\_index.md as the entry point
- Write one contract file per flow
- Required sections: Happy Path, Failure Modes, Edge Cases, Concurrency Rules, Constraints
- Use N/A when a section doesn't apply; TODO blocks status: active

Flag anything underspecified. Wait for review before writing any DDA docs.

---

### Bootstrap Scaffold from PRD

Attach the PRD + approved flow and contract files, then send:

Given this PRD and the approved flows and contracts, generate the Playbook scaffold.

- Produce \_index.md for the project and each module with living folder trees
- Create stub docs for all identifiable components, sequencers, concerns, integrations
- Mark all stubs as status: draft
- For sequencers, include a stub Flow section referencing the relevant flow files
- Flag anything underspecified

---

### Add New Component

Attach the parent \_index.md + relevant flow file, then send:

Given this parent doc and flow, create a Playbook [type] doc for [description].
Follow Playbook conventions. Flag any missing relationships.
If type is sequencer, include a Flow section with full call chain and all branches.
After creating the doc, provide the updated Structure section for the parent \_index.md.

---

### Sync Check

Send before every PR:

Run a Playbook sync check on all files touched this session.
For each: does the code match the Playbook doc?
List any mismatches. Update affected docs.
Update the living folder tree in any \_index.md where components changed.

---

### Generate Rollout Doc

Attach all .playbook/ docs + .kit/04-progress.md, then send:

Generate a rollout doc for [milestone name].
Read the full .playbook/ tree and 04-progress.md.
Output to docs/rollout/[milestone].md.

---

### Adopt Existing Project

Upload .playbook/00-rules.md + docs/code-standards.md, then send:

I am adopting the Playbook pipeline for an existing project.
Read these two files first. Confirm you have read them. Do not begin any work yet.

Then follow the decision tree in .playbook/.guides/adopt.md.

---

### Reverse Engineer -- Start Inventory

Upload .playbook/00-rules.md + all available project context, then send:

The Reverse Engineer expansion is active. Current phase: inventory.

Run an inventory of this codebase.

Map and document:

1. All applications or services and what they do
2. All major modules, components, and services within each app
3. All external integrations and what they provide
4. The data model -- what data exists and how it is structured
5. Entry points -- how the system is accessed
6. Dependency relationships -- what depends on what
7. Tech stack -- frameworks, libraries, tools, versions

Produce a draft system-map.md. Use plain language.
Flag anything unclear or needing investigation.
Wait for review before proceeding.

---

### Reverse Engineer -- Advance Phase

Send when advancing to the next phase:

RE expansion current phase: [inventory | audit | classify | risk-map | gap-report | handoff]
Previous phase output reviewed and approved.

Proceed to [next phase].
[Attach relevant rewrite docs]

Follow the phase instructions in .playbook/.guides/expansions/reverse-engineer.md.
Wait for review before advancing again.

---

### Reverse Engineer -- Handoff

Attach all docs/rewrite/ files + current kit files, then send:

RE expansion is complete. Run the handoff.

1. Extract open topics from system-map.md for .kit/02-exploration.md
2. Extract closed decisions from decision-record.md for .kit/02-exploration.md
3. Extract known issues from risk-register.md for .kit/03-execution.md
4. Update .kit/01-process.md -- mark RE expansion complete, update Current State

Generate updated kit files as markdown code blocks for review.

---

### Playbook End of Session

Before we close:

1. Run Playbook sync check on all docs touched this session
2. Update .kit/04-progress.md -- completions, decisions, fixes
3. Update Current State in .kit/01-process.md
4. Update living folder tree if components changed
   Generate updated markdown for all changed kit files.

---

### Error Recovery

Something has gone wrong. Stop all other work.

1. What broke: [description]
2. Category: type error | test failure | Playbook drift | layer violation | other
3. Location: code | Playbook doc | both
4. Fix at source -- do not patch symptoms
5. After fixing: re-run checks and Playbook sync
6. Log as Fixed entry in .kit/04-progress.md

---

### Playbook Context Rot Recovery

Stop. Your only source of truth is the files uploaded at session start.
Ignore all prior conversation context.
Re-read .playbook/00-rules.md and docs/code-standards.md before continuing.

---

---

### Video Insights -- Extract with Gemini

Send to Gemini (not Claude) with the video URL:

Please summarize the main arguments in '[Video Title]' by [Channel Name].
Use the transcript to highlight the most important takeaways.
[URL]

Structure your response using this template:

---

title: "[Video Title]"
source: "[Channel Name]"
url: [URL]
date: [Today's Date]
type: [general | tutorial | case-study | framework | opinion | research]
topics: [2-4 relevant tags]
relevance:

---

Generate the code in markdown: `markdown ...`.

## Core Idea

One paragraph. The central argument or insight.

## Key Points

3-5 most important takeaways. No more.

## Connections

Leave blank.

## Status

Pending

Output as a downloadable markdown file.

For tutorials add: ## Steps
For case studies add: ## Outcome
For frameworks add: ## Framework
For opinion pieces add: ## Counterarguments
For research add: ## Evidence

Save output to: .kit/\_refs/video-insights/[kebab-title].md

---

### Video Insights -- Discuss in Playbook

Upload the insight file alongside your kit files, then send:

I have a new video insight to discuss.
File: .kit/\_refs/video-insights/[filename].md

Review it and:

1. Summarize the core idea in one sentence
2. Identify connections to our current project context
3. Flag any conflicts with closed decisions in 02-exploration.md
4. Suggest whether this should open a topic, close a decision,
   add a task, or surface a risk
5. Ask me how I want to proceed

Do not integrate anything yet. Surface connections and wait.

---

### Video Insights -- Integrate

After discussion, if integrating:

Integrate [insight title] as follows:

- [Open topic | Closed decision | Task | Known issue]
- Ref: .kit/\_refs/video-insights/[filename].md
  Update the insight file Status to: Integrated
  Log in 04-progress.md: Decided: [title] -- Integrated

---

### Session Limit Warning

We are approaching the session limit.
Update all kit files and any changed Playbook docs now.
Generate markdown for each so I can copy-paste and save before we continue.
