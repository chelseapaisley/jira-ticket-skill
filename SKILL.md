---
name: jira-ticket-skill
description: Use this skill when the user wants to set up a personalised Jira ticket writer, capture their Jira style, or build a skill that writes tickets in their format. Trigger when the user says things like "set up my Jira skill", "learn my ticket style", "create a Jira ticket writer for me", or "build me a Jira skill". This skill analyses the user's existing tickets and produces a tailored, ready-to-install jira-ticket-writer.md. It does NOT write individual tickets — that's what the output skill is for.
---

# Jira Ticket Skill — Setup

This skill does one thing: analyses the user's existing Jira tickets, presents optional standards for them to choose from, tests against real briefs with a visible feedback loop, and outputs a personalised `jira-ticket-writer.md` skill they can install and use to write tickets from a brief.

---

## Step 1 — Choose a data source

Ask the user:

> "To learn your ticket style, I need to look at some of your existing Jiras. How would you like to provide them?"
>
> **Option 1 — Jira MCP**: If you have Jira MCP connected, I can either search your recent tickets automatically, or fetch specific tickets by ID if you want to cherry-pick your best examples.
>
> **Option 2 — Rovo**: I'll give you a prompt to run in Rovo. It will analyse your tickets and bring back a structured summary — you paste that summary here and we take it from there.
>
> **Option 3 — Paste your best ones**: Pick 5–10 tickets you're happy with and paste them in directly.

---

## Step 2 — Fetch and analyse

### If MCP

Ask the user:

> "Do you want me to pull your most recent tickets automatically, or would you prefer to give me specific ticket IDs to cherry-pick your best examples?"

**If automatic search**: Run `creator = currentUser() AND created >= -90d ORDER BY created DESC`, fetch up to 20 tickets including summary, description, labels, components, priority, issue type, and any populated custom fields.

**If specific IDs**: Ask the user to provide the ticket IDs (e.g. PROJ-123, PROJ-456). Fetch each one individually via MCP, collecting the same fields as above.

Either way, confirm how many tickets you retrieved and what mix of types, then do the pattern extraction yourself (see Step 3).

### If Rovo

Give the user the following prompt to paste into Rovo verbatim. Rovo will do the analysis and return a structured summary — the user pastes that back here and you skip to Step 4.

---

**[Rovo prompt — give this to the user to copy]**

```
Search for Jira tickets I created in the last 3 months using:
  creator = currentUser() AND created >= -90d ORDER BY created DESC

Retrieve up to 20 tickets. For each ticket, collect: summary, description, 
issue type, labels, components, priority, story points (if set), and any 
custom fields that are filled in.

Then analyse all tickets and extract the following as RULES (not observations 
— write "Summaries must..." not "Summaries tend to..."):

SUMMARY RULES
- Does it start with a verb? Which verbs are used?
- What is the typical length?
- Is there a prefix or naming convention?
- Do bugs look different from stories in the summary line?

DESCRIPTION RULES
- What sections appear consistently? List them in the order they appear.
- Prose or bullet points?
- Is there a user story line ("As a... I want... So that...")?
- How long is a typical description?

FIELD RULES
- Which labels appear in more than half of tickets?
- What is the default priority?
- Are components consistently filled in?
- Is story point estimation used? What scale?
- Which custom fields are regularly populated?

TICKET TYPE DIFFERENCES
- How do bugs differ structurally from stories?
- Are there sub-task or task conventions?

TONE AND LANGUAGE
- Formal or conversational?
- How technical is the language?
- How are open questions or unknowns handled?

Return only the structured rule output — no preamble, no ticket content, 
no commentary. Format each section with its heading followed by bullet-point rules.
```

---

Tell the user: *"Paste the output from Rovo back here and we'll continue."*

When they paste it back, treat the Rovo output as the completed pattern extraction and proceed to Step 4.

### If manual paste

Accept whatever they provide. Confirm how many tickets you received and what types, then do the pattern extraction yourself (Step 3).

---

## Step 3 — Extract patterns (MCP and manual paths only)

Analyse the tickets and extract the following as **rules**, not observations. Write "Summaries must start with an imperative verb" not "Summaries tend to start with verbs." This extraction is what gets embedded into the final skill.

#### Summary rules
- Verb first? Which verbs?
- Typical length?
- Prefix or naming convention?
- Do bugs differ from stories in phrasing?

#### Description structure rules
- Which sections appear consistently, and in what order?
- Prose or bullets?
- User story format used?
- Typical description length?

#### Field rules
- Labels that appear in most tickets
- Default priority
- Components: always filled, sometimes, never?
- Story points: used? what scale?
- Custom fields regularly populated

#### Ticket type differences
- How do bugs differ structurally from stories?
- Sub-task or task conventions?

#### Tone and language
- Formal or conversational?
- How technical?
- How are unknowns handled?

---

## Step 4 — Present standards for the user to choose from

Read `references/standards.md` for the full details of each standard.

Then present the following to the user — briefly describe each one and ask which they want included in their skill. Don't pre-select anything.

> "Here are some optional standards I can bake into your ticket writer. Tell me which ones matter for your workflow — pick as many or as few as you like."

Present these options:

**1. Accessibility (a11y)**
Adds an `a11y` label to UI-touching tickets and prompts for screen reader behaviour, keyboard nav, focus management, and whether states are distinguishable without colour alone. Good for product teams shipping user-facing features.

**2. Security & privacy**
Prompts for data handling, permissions, PII, and input validation considerations where relevant. Good for teams working with user data or auth flows.

**3. Failure & edge cases**
Ensures every ticket covers loading states, empty states, error states, and boundary conditions. Good for any team — this one is almost always missing.

**4. Acceptance criteria standards**
Enforces verifiable AC items per ticket, written as testable behaviour statements. If you can only find 1–2, the ticket is probably underspecified — the standard prompts Claude to push on the brief rather than pad.

**5. Story point estimation**
Includes an estimate with rationale on every ticket, using a standard Fibonacci scale if you don't have your own. Good for teams running sprints.

Wait for the user's response before proceeding.

---

## Step 5 — Test with real briefs and iterate

Tell the user:

> "Before I generate your skill, I'd like to test my understanding against a couple of real examples. Give me 2–3 briefs — anything you'd normally turn into a Jira. Rough notes, a Slack message, a description of a bug — whatever form they come in."

For each brief:
1. Infer the ticket type and state why in one line
2. Draft a complete ticket using the extracted patterns and chosen standards
3. Show it to the user

After each draft, ask:

> "Does this look right? Tell me anything that's off — wrong format, wrong tone, missing something, field that shouldn't be there, etc."

When the user gives feedback, do two things:

**1. Fix the draft.**

**2. Propose the rule it implies, explicitly:**

> "I'll update my understanding: [proposed rule in plain language]. Does that sound right as a general rule, or was this a one-off?"

Wait for confirmation before treating it as a rule. If the user refines it, use their version. If they say it's a one-off, note that and don't generalise it.

Keep a running **Rule Log** — shown to the user as it grows:

```
✓ Bug summaries start with "Fix:" not an imperative verb
✓ Story points are omitted for bugs — only stories get estimated
✓ Components field is always left blank — team doesn't use it
```

After all test briefs are done, show the complete rule log:

> "Here are all the rules I've derived — from your tickets and from our feedback loop. Review and edit anything before I generate your skill."

Let the user edit the rule log directly. Proceed only when they confirm it's right.

---

## Step 6 — Generate the personalised skill

Output a complete, copy-pasteable markdown file. Tell the user:

> "Here's your personalised Jira ticket writer. Save this as `jira-ticket-writer.md` and install it as a Claude skill — then just paste any brief and it'll write tickets in your style."

The generated file must follow this structure, fully filled in with the user's actual patterns:

```markdown
---
name: jira-ticket-writer
description: Write Jira tickets in my style from a brief or rough description. 
Trigger when asked to write, draft, or create a Jira ticket, story, or bug report, 
or when given a brief and asked to turn it into a ticket.
---

# Jira Ticket Writer

Write tickets that match my format and conventions exactly, with the standards 
I've chosen applied consistently.

## How to handle a brief
Take whatever the user gives — rough notes, a Slack message, bullet points — 
and produce a complete ticket without asking clarifying questions first. 
Make a reasonable call on anything ambiguous and flag it at the end.

Infer the ticket type (story/bug/task) from the brief. State what you inferred 
and why in one line before the ticket.

## Ticket types
[story vs bug vs task — structural differences extracted from their tickets]

## Summary rules
[rules extracted from their tickets]

## Description format

### Stories
[their actual section structure, in order, with notes on length/style]

### Bugs
[their actual section structure for bugs — or note if same as stories]

## Field defaults
- **Labels**: [their common labels]
- **Priority**: [their default and when to escalate]
- **Components**: [how to infer, or note if always left blank]
- **Story points**: [their scale, or omit if not used]
- [any custom fields they consistently fill]

## Language and tone
[their conventions — formal/casual, technical depth, how unknowns are handled]

## Standards
[only the standards the user selected, pulled from references/standards.md 
and written naturally into the skill — not as a bolt-on checklist]

## Output format
Output the ticket as a clean markdown block with labelled fields, ready to 
paste into Jira. After the ticket, add a "---" followed by a single line 
noting anything ambiguous in the brief that the user should decide before filing. 
If nothing is ambiguous, omit this section entirely.

If Jira MCP is connected, end with: "Want me to file this in Jira?"
```

Write the generated skill in first person ("my style", "I use", "my team") — it should read like the user wrote it themselves, not like a template someone filled in.

Do not include any section headers or placeholder text from the template above that don't apply to this user. If they don't use story points, omit that field. If bugs and stories have the same structure, say so and use one template.
