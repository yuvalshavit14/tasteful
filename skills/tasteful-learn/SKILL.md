---
name: tasteful-learn
description: "Add a new principle to the project knowledge base, or learn from a correction. Use when the user says 'tasteful learn', 'learn this', 'add principle', 'remember this convention', or corrects a decision with 'that was wrong, it should be...'."
---

# Learn — Add or Update Project Principles

You manage the project's knowledge base of principles. Principles are reusable conventions and decisions that guide all future development work via `/tasteful-implement`.

## What Triggers This

1. **Developer explicitly adds a principle**: "Learn that we always use bottom sheets instead of modals on mobile"
2. **Developer corrects a past decision**: "That was wrong — we should use server actions, not API routes for mutations"
3. **Developer wants to generalize from a specific case**: "Based on what we just did, add a principle about..."
4. **Post-session review**: After `/tasteful-implement` completes, the developer reviews developer-sourced decisions and wants to promote some to principles. The developer may paste or reference specific decision IDs from decisions-log.json.

## Process

### Step 1: Understand the Input

Read what the developer said. Determine:
- Is this a new principle to add?
- Is this a correction to an existing principle?
- Is this a specific case that should be generalized?

### Step 2: Read Existing Principles

Read `principles.json` from the project's memory directory. Parse the JSON array to:
- Check for duplicate or conflicting principles
- See what domains already exist (prefer existing domains over creating new ones)
- Understand the current knowledge base state

If the file doesn't exist, create it as an empty JSON array: `[]`

### Step 3: Formulate the Principle

If the developer gave a specific case (e.g., "we should have used X instead of Y for the settings page"):
- **Generalize it**: Extract the underlying rule that applies beyond this specific case
- **Keep it actionable**: Write it as a directive, not a description
- **Keep it specific enough**: "Always use server actions for form mutations" is better than "Use good patterns"

Present the formulated principle to the developer for confirmation:
"Based on your input, I'd add this principle under [domain]: '[principle text]'. Does that capture it correctly?"

### Step 4: Save the Principle

Read `principles.json` and parse the JSON array. Then:

**For a new principle**, append a new object to the array:
```json
{
  "id": "p-NNN",
  "domain": "[Domain]",
  "principle": "[Principle text]",
  "source": "learned from [context]",
  "status": "active",
  "createdAt": "YYYY-MM-DD"
}
```
Generate the ID based on the array length (e.g., if 10 principles exist, next is `p-011`).

**If correcting an existing principle**, find the matching object and update it:
```json
{
  "id": "p-003",
  "domain": "[Domain]",
  "principle": "[Updated principle text]",
  "source": "corrected from '[old text]' — [reason]",
  "status": "active",
  "createdAt": "YYYY-MM-DD"
}
```

Write the full JSON array back to `principles.json`.

### Step 5: Confirm

Tell the developer what was saved and which domain it was filed under.

## Batch Mode (Post-Session Review)

When the developer wants to review recent decisions and promote some to principles:

### Step 1: Read Recent Decisions

Read `decisions-log.json` from the project's memory directory. Filter for decisions with `source: "developer"`. If the developer specifies a date range or feature, filter further.

Present the filtered decisions:
```
Recent developer decisions:
1. d-012: "Use bottom sheet for character picker" (UX, from feature: template-flow)
2. d-015: "Show inline error, not modal, for failed uploads" (UX, from feature: photo-upload)
3. d-018: "Free users see locked state, not hidden" (Business Logic, from feature: paywall)
```

Ask which ones (if any) should become principles.

### Step 2: Generalize Selected Decisions

For each selected decision, generalize it into a principle:
- d-012 → "Use bottom sheets (not modals) for selection UIs on mobile"
- d-015 → "Show errors inline near the failed action, not in a modal"
- d-018 → "Show locked/gated state for premium features — never hide them from free users"

Present for confirmation, then save each to principles.json per the normal Step 4 process.

## Principle Quality Guidelines

**Good principles are:**
- **Actionable**: "Use Framer Motion for page transitions" (not "Animations are important")
- **Specific**: "Mobile-first using Tailwind sm/md/lg breakpoints" (not "Be responsive")
- **Grounded**: Based on actual code patterns or explicit developer decisions
- **Non-obvious**: Capture decisions that a new developer would not automatically make correctly

**Bad principles:**
- Generic truisms: "Write clean code", "Test your code", "Handle errors"
- Too vague: "Good UX", "Fast performance"
- Too specific: "The login button should be 44px tall" (one-off detail, not a principle)

## Domain Examples

Common domains (use existing ones from the file when possible):
- **Architecture**: Code organization, component structure, data flow patterns
- **Styling**: CSS approach, design tokens, responsive strategy
- **UX**: Interaction patterns, navigation, feedback, mobile patterns
- **API Design**: Endpoint conventions, error formats, data fetching
- **Data**: Database patterns, schema conventions, state management
- **Testing**: Test strategy, what to test, how to test
- **Performance**: Loading strategy, bundle optimization, caching
- **Auth**: Authentication, authorization, session patterns
- **Accessibility**: ARIA patterns, keyboard navigation, screen reader support
