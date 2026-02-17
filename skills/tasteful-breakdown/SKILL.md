---
name: tasteful-breakdown
description: "Break down a high-level goal into independent, implementable features. Use when the user provides a broad goal like 'add user settings' or 'build the checkout flow' and says 'tasteful breakdown', 'break this down', or needs decomposition into concrete tasks."
---

# Feature Breakdown

You decompose a high-level goal into independent, implementable features that can each be built in a focused session using `/tasteful-implement`.

## Process

### Step 1: Understand the Goal

Read what the developer wants to build. If it's vague, ask clarifying questions using `AskUserQuestion` before proceeding.

### Step 2: Read Product Context

Read from the project's memory directory:
- `product-info.md` — product purpose, target users, existing features, UX principles, design language, tech stack
- `principles.json` — established conventions and patterns (JSON array of principle objects)

If these files don't exist, tell the developer to run `/tasteful-onboard` first.

### Step 3: Explore Relevant Code

Use Glob and Read to understand:
- Existing code related to the goal
- Current data model and API structure
- Component patterns that new features should follow
- Any partial implementations or TODO comments

### Step 4: Decompose

Break the goal into features. Each feature should be:

- **Independent**: Can be built without waiting for other features (or has clear, minimal dependencies)
- **Implementable**: Specific enough that a focused `/tasteful-implement` session can complete it
- **Complete**: Includes everything needed (UI, API, data model changes) or clearly scopes what's included
- **Testable**: Has a clear "done" state that can be verified

For each feature, provide:
- **Name**: Short descriptive name (2-4 words)
- **Description**: What to build (2-3 sentences). Be specific — this becomes the starting brief for `/tasteful-implement`.
- **Type**: "frontend", "backend", "fullstack", or "devops"
- **Dependencies**: Which other features (if any) must be built first
- **Estimated complexity**: Small / Medium / Large

### Step 5: Present and Refine

Present the breakdown:

```
## Feature Breakdown: [Goal]

### 1. [Feature Name] (type: backend, complexity: small)
[Description]
Dependencies: none

### 2. [Feature Name] (type: frontend, complexity: medium)
[Description]
Dependencies: #1

### 3. [Feature Name] (type: fullstack, complexity: large)
[Description]
Dependencies: none
```

Ask if they want to:
- Adjust scope (split large features, merge small ones)
- Change priorities or dependencies
- Add or remove features
- Proceed to implementation

### Step 6: Implementation Path

Once the developer approves, suggest the implementation order based on dependencies and recommend whether features can be built in parallel.

**Sequential (single session):**
```
/tasteful-implement [Feature 1 description]
/tasteful-implement [Feature 2 description]
```

**Parallel (team):**
Run `/tasteful-implement` with all features — it will create a team with a PM agent and one engineer per feature. The PM handles product/UX decisions (checking product-info and principles before bothering the developer). Each engineer gets its own isolated git worktree via `superpowers:using-git-worktrees` and follows the full superpowers engineering chain.

## Decomposition Guidelines

### Right Granularity
- Too big: "Build the entire user dashboard" → should be 3-5 features
- Too small: "Add a CSS class for the badge" → this is a task within a feature
- Just right: "Build the settings page with profile editing and avatar upload"

### Think About
1. What are the truly independent pieces of work?
2. Are there data model changes that must happen before UI work?
3. Can the UI and API be built independently and connected later?
4. Does each feature have a clear "done" state?
5. Together, do the features fully cover the goal?

### Common Patterns
- Backend API + Frontend UI are often separate features (API first, then UI)
- Database migrations should be their own feature if significant
- Auth/permissions layer is usually its own feature
- Testing infrastructure setup is its own feature if it doesn't exist yet
