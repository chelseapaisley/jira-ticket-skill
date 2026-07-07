# Example: Brief → Ticket

This shows the output skill in use, given a rough brief.

---

## Input brief

> dark mode toggle in settings isn't remembering the user's preference after they log out and back in. seems like we're not persisting to the db, just local storage. been getting support tickets about it

---

## Output ticket

**Type:** Bug *(inferred — describes broken existing behaviour with a likely cause)*

---

```
Summary: Fix dark mode preference not persisting across logout/login

Type: Bug
Priority: P2
Labels: a11y, dark-mode, preferences, user-settings
Components: Frontend, Backend
Story Points: 3

---

**What's happening**
The dark mode toggle in user settings is not retained after the user logs out and logs back in. On return, the UI reverts to light mode regardless of the user's saved preference.

**Likely cause**
Preference appears to be stored in localStorage only, not persisted to the user record in the database. localStorage is cleared on logout.

**Steps to reproduce**
1. Log in
2. Navigate to Settings → Appearance
3. Enable dark mode
4. Log out
5. Log back in
6. Observe: UI is in light mode

**Expected behaviour**
Dark mode preference persists across sessions. User's chosen appearance is restored on login.

**Actual behaviour**
Preference reverts to light mode on every logout/login cycle.

---

**Accessibility**
- Ensure the preference toggle itself remains keyboard accessible and that its state (on/off) is correctly announced by screen readers after this fix
- Confirm dark mode and light mode states are distinguishable by more than colour alone (e.g. a label or icon alongside the visual change)

**Security considerations**
- This is a user preference stored against their account — confirm it's scoped to the authenticated user and not readable across accounts
- No PII involved beyond user ID

---

**Acceptance criteria**
- [ ] Dark mode preference is persisted to the database on toggle
- [ ] On login, UI applies the user's saved preference before first render (no flash of wrong theme)
- [ ] Preference is correctly restored across different devices/browsers for the same account
- [ ] Toggle state is correctly announced by screen reader ("Dark mode on" / "Dark mode off")
- [ ] Light mode preference is also correctly persisted (not just dark)

**Failure & edge cases**
- Loading: show the user's previous preference optimistically on login to avoid a flash of the wrong theme while the DB preference loads
- Error: if preference fetch fails on login, default to light mode silently — don't block login
- No saved preference (new account or pre-migration): default to light mode
- Preference should update immediately on toggle — no save button required

---

**Notes**
Existing localStorage logic can likely remain as a fast-read cache, with DB as the source of truth on login. Worth checking if other user preferences have the same issue.

Estimation: 3 points — clear scope, one likely root cause, but touches both FE and BE and needs migration consideration for existing users with no DB preference saved.
```

---

*Ambiguous / needs decision before filing: is there a preference migration needed for existing users? Their localStorage preference won't be in the DB. Worth deciding whether to default them to light mode or attempt a best-effort migration.*

---

*Want me to file this in Jira?*
