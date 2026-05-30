# Video Insights Expansion
# File: .playbook/.guides/expansions/video-insights.md

The Video Insights expansion connects external video content to the Playbook
pipeline. It lets you extract insights from any video during any project stage,
evaluate them in context, and integrate the relevant ones into the kit.

Activate in .kit/01-process.md when you have insights to discuss:

  Active Expansions: video-insights

This expansion can fire at any point in the pipeline -- not tied to a stage.

---

## Why This Exists

Good ideas come from everywhere. A video watched mid-project can challenge
an existing decision, open a new direction, or validate a current approach.

Without a system, those insights either get lost or get imported too quickly
without evaluation. This expansion gives them a proper home and a proper
process -- extract, discuss, decide, then integrate or discard.

---

## The Flow

  Watch video
       |
  Extract with Gemini -> .kit/_refs/video-insights/[title].md
       |
  Open discussion in Playbook session
       |
  Evaluate -- relevant? conflicts with closed decisions? opens new questions?
       |
  Decision:
    Integrate -> promotes to 02-exploration.md (open topic or closed decision)
                 or adds task to 03-execution.md
                 or surfaces risk in Known Issues
    Discard   -> stays in _refs/ as reference, Status: Discarded
    Defer     -> stays in _refs/ as reference, Status: Pending

---

## Step 1 -- Extract with Gemini

Use the Gemini prompt below. Gemini handles extraction.
Playbook handles the thinking.

### Base Prompt

Copy and customize -- replace bracketed values:

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

  ## Core Idea
  One paragraph. The central argument or insight.

  ## Key Points
  3-5 most important takeaways. No more.

  ## Connections
  Leave blank.

  ## Status
  Pending

  Output as a downloadable markdown file.

### Variant Prompts

For specific content types, add one section after Key Points:

  Tutorial / How-to -> add: ## Steps (the process in order)
  Case Study        -> add: ## Outcome (what happened and what it proved)
  Framework/System  -> add: ## Framework (the model, how it works, how applied)
  Opinion           -> add: ## Counterarguments (objections and limitations)
  Research/Data     -> add: ## Evidence (data points and what they actually show)

---

## Step 2 -- Save to _refs/

Save the Gemini output to:

  .kit/_refs/video-insights/[kebab-title].md

Naming: kebab-case of the video title. Keep it short and recognizable.

Example:
  youtube-is-a-game-think-media.md
  atomic-habits-james-clear.md

---

## Step 3 -- Open Discussion in Playbook

Upload the insight file alongside your kit files in a new or current session.

Use this prompt:

  I have a new video insight to discuss.
  File: .kit/_refs/video-insights/[filename].md

  Review it and:
  1. Summarize the core idea in one sentence
  2. Identify any connections to our current project context
  3. Flag any conflicts with closed decisions in 02-exploration.md
  4. Suggest whether this should open a new topic, close a decision,
     add a task, or surface a risk
  5. Ask me how I want to proceed

  Do not integrate anything yet. Just surface the connections and wait.

---

## Step 4 -- Decide

After the discussion, make one of three decisions:

### Integrate

Promote the insight to the appropriate kit location:

  Opens a question ->
    Add to 02-exploration.md ## Open Topics
    Ref: .kit/_refs/video-insights/[filename].md

  Confirms or changes a decision ->
    Add to 02-exploration.md ## Closed Decisions
    Ref: .kit/_refs/video-insights/[filename].md

  Adds a task ->
    Add to 03-execution.md ## Action tasks

  Surfaces a risk ->
    Add to 03-execution.md ## Known Issues

  Update the insight file:
    Status: Integrated
    Connections: [what was integrated and where]

### Discard

The insight is not relevant. Update the file:
  Status: Discarded

It stays in _refs/ as a record. Never deleted.

### Defer

Not relevant now but may be later. Update the file:
  Status: Pending

Review again when the project context changes.

---

## Step 5 -- Log

Append to 04-progress.md:

  Decided: [insight title] -- [Integrated | Discarded | Deferred]
  [One line on what was integrated or why it was discarded]

---

## Rules

- Gemini extracts. Playbook connects. Never skip the discussion step.
- Never integrate directly from the insight file without AI evaluation first
- Connections and relevance fields are filled during Playbook session -- not by Gemini
- Discarded insights stay in _refs/ -- never deleted
- Every integrated insight is traceable via Ref: in 02-exploration.md
- Log every decision in 04-progress.md

---

## File Locations

  Templates:    .kit/_refs/video-insights/video-insight-base.md
  Variants:     .kit/_refs/video-insights/video-insight-variants.md
  Insights:     .kit/_refs/video-insights/[kebab-title].md
  Prompts:      .kit/00-prompts.md (Video Insights section)
