# Standards Reference

This file is read by the setup skill when presenting options to the user, and embedded 
(for chosen standards only) into the generated jira-ticket-writer.md.

Each standard below includes: what it covers, when it's relevant, and how to write it 
into the generated skill naturally rather than as a bolt-on checklist.

---

## 1. Accessibility (a11y)

**What it covers**
Accessibility considerations surfaced in the ticket description — screen reader 
behaviour, keyboard nav, focus management, contrast — so developers know what to 
implement, not just that accessibility matters.

**When it's relevant**
Product teams shipping user-facing features. Less relevant for internal tooling, 
infra, or teams with no UI surface.

**How to embed in the generated skill**

Add to the generated skill's Standards section:

```
### Accessibility
- Tag any ticket that touches UI with the Jira label `a11y`. This is a plain 
  text label on the ticket (same as labels like "frontend" or "backend"), used 
  to filter and track accessibility work across the board. Apply to direct UI 
  changes and indirect touches (an API change that affects what's displayed, 
  a data change that surfaces in a component).
- For UI-touching tickets, include an Accessibility section in the description 
  covering whichever of the following are relevant to that ticket:

  **Screen reader behaviour** — what should be announced for this element 
  or interaction? What about dynamic state changes (loading, error, expanded)?
  Example AC: "Screen reader announces 'Loading results' during search and 
  'N results found' on completion"

  **Keyboard navigation** — can this be fully used without a mouse? What's 
  the tab order? Any focus traps?
  Example AC: "User can navigate options with arrow keys; Enter selects; 
  Escape closes and returns focus to the trigger element"

  **Focus management** — after an action (modal open/close, form submit, 
  route change), where does focus land?
  Example AC: "On modal close, focus returns to the button that opened it"

  **Visual** — are interactive elements and states (error, selected, disabled) 
  communicated through something other than colour alone? (e.g. an icon, label, 
  pattern, or shape — not just a red border or green highlight)

  Only include subsections relevant to the ticket. A small backend change 
  with a minor UI impact doesn't need all four.
```

---

## 2. Security & Privacy

**What it covers**
Prompting the ticket writer to surface data handling, permission, and input 
validation implications that are easy to miss in a brief.

**When it's relevant**
Teams working with user data, auth flows, permissions, or any external inputs. 
Less relevant for pure UI/design tickets or internal tooling with no sensitive data.

**How to embed in the generated skill**

```
### Security & privacy
For any ticket involving user data, auth, permissions, or external inputs, 
include a Security considerations section covering whichever apply:
- What data does this touch? Is any of it PII or sensitive?
- Permission/role implications — who should and shouldn't be able to do this?
- Input validation concerns?
- Changes to how data is stored, transmitted, or logged?
- Anything that needs a security review before shipping?

If none of these apply (e.g. a pure cosmetic UI change), omit the section 
entirely — don't write "N/A".
```

---

## 3. Failure & Edge Cases

**What it covers**
Loading states, empty states, error states, and boundary conditions — the things that are almost 
always missing from tickets but that developers and QA have to make decisions about 
anyway, often inconsistently.

**When it's relevant**
Almost always worth including. This is the standard most universally applicable 
regardless of team or product type.

**How to embed in the generated skill**

```
### Failure & edge cases
Every ticket must address failure and edge conditions, either in a dedicated 
section or folded into AC items:

- **Loading state** — what does the user see while data is fetching or an 
  action is in progress? Is the UI blocked? Is there a spinner, skeleton, 
  or inline indicator?
- **Empty/null state** — what does the user see if there's no data? 
  (empty list, new user, zero results)
- **Error state** — what happens if the API fails or times out? Is the 
  error message actionable? Is it logged?
- **Boundary conditions** — max length inputs, zero values, very long strings, 
  special characters, unexpected but not unreasonable user behaviour

If the ticket is clearly not affected by any of these (e.g. a config change 
with no user surface), omit the section.
```

---

## 4. Acceptance Criteria Standards

**What it covers**
Enforcing that AC is explicit, verifiable, and thorough enough to be useful 
for both development and QA.

**When it's relevant**
Teams where tickets go directly to developers, or any team where what counts 
as finished is sometimes disputed. Less critical if the team has a separate spec process.

**How to embed in the generated skill**

```
### Acceptance criteria
AC must be:
- Written as verifiable behaviour statements, not goals 
  ("User sees a confirmation toast" not "Good feedback")
- Specific enough that a developer and QA engineer would independently 
  agree on whether it's met
- Written from the user or system perspective, not the implementation perspective

If you can only find 1–2 AC items, don't pad — but do push on the brief. 
A ticket with very few AC items is usually a sign the scope isn't clear enough 
yet, not that the work is genuinely that simple.
```

---

## 5. Story Point Estimation

**What it covers**
An estimate with brief rationale on every ticket, so sprint planning doesn't 
require a separate estimation session.

**When it's relevant**
Teams running sprints with story point estimation. Skip for kanban teams, 
teams that do estimation in a separate ceremony, or teams that don't point.

**How to embed in the generated skill**

```
### Story point estimation
Include a story point estimate with one-line rationale on every ticket.

Scale:
- 1 — Trivial, well-understood, no unknowns (config change, copy update)
- 2 — Small and clear, minimal risk
- 3 — Standard work, some complexity
- 5 — Meaningful complexity or some unknowns
- 8 — Large scope or significant unknowns — consider splitting before pointing
- 13+ — Should be broken down first

Always flag if a ticket feels too large to point with confidence.
```
