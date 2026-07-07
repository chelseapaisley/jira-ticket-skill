# Example: Brief → Ticket (Story)

This shows the output skill in use, given a rough brief for a new feature.

---

## Input brief

> users need to be able to export their activity history as a CSV. comes up a lot in support, mostly from enterprise customers who want to pull it into their own reporting tools

---

## Output ticket

**Type:** Story *(inferred — describes new user-facing functionality)*

---

```
Summary: Add CSV export for user activity history

Type: Story
Priority: P2
Labels: a11y, export, activity, enterprise
Components: Frontend, Backend

---

**Why**
Enterprise customers frequently request access to their activity history for 
use in internal reporting tools. Currently there is no export option, leading 
to repeated support requests.

**What**
Add a CSV export action to the activity history page. Scoped to the current 
user's own history only. Out of scope: filtering before export, scheduled/automated 
exports, other file formats.

**Acceptance criteria**
- [ ] User can trigger a CSV export from the activity history page
- [ ] Export contains all activity history for the authenticated user
- [ ] Downloaded file is named clearly (e.g. activity-history-2026-06-29.csv)
- [ ] Column headers are human-readable, not raw field names

**Failure & edge cases**
- Loading: show a loading indicator while the export is being generated —
  disable the export button to prevent duplicate requests
- Empty state: if the user has no activity history, either disable the export 
  button with a tooltip ("No activity to export") or export a CSV with headers 
  only — decide which before filing
- Error: if export generation fails, show an inline error message with an 
  option to retry — do not silently fail or download an empty file
- Large history: if the dataset is very large, consider whether this should 
  be generated async with a download link emailed to the user rather than 
  a synchronous download

**Accessibility**
- Export trigger must be keyboard accessible and labelled clearly for screen 
  readers (not just an icon button)
- Loading and error states must be announced to screen readers, not just 
  shown visually
- Confirm the trigger and any status indicators are distinguishable by more 
  than colour alone

**Security considerations**
- Export must be scoped strictly to the authenticated user's own data — 
  no ability to export another user's history via parameter manipulation
- Confirm whether activity history contains anything that should be excluded 
  from export (e.g. internal metadata, admin actions, deleted content)
- Ensure the download is served with appropriate headers to prevent caching 
  of the file in the browser
```

---

*Ambiguous / needs decision before filing: what happens when a user with a very large activity history exports — synchronous download or async with email link? Also worth confirming what "activity" includes (logins only? all actions? admin-visible events?) before scoping the backend query.*

---

*Want me to file this in Jira?*
