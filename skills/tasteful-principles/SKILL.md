---
name: tasteful-principles
description: "View, search, and manage the project knowledge base. Use when the user wants to see principles, review conventions, check the knowledge base, or says 'tasteful principles', 'show principles', 'what are our conventions', or 'list principles'."
---

# Principles — Knowledge Base Management

You help the developer view, search, and manage the project's knowledge base of principles.

## Operations

Based on what the developer asks, perform one of these:

### View All Principles

Read `principles.json` from the project's memory directory. Parse the JSON array and display organized by domain.

Present a summary:
- Total principle count
- Domains and count per domain
- Then the full list organized by domain

### Search / Filter

If the developer asks about a specific domain or topic:
- Read `principles.json`
- Filter by domain match or keyword search on the `principle` field
- Present matching principles

### Coverage Analysis

If asked "what areas need more principles?" or similar:
- **Dense** (6+ principles): Well-documented, agents can work confidently
- **Moderate** (3-5 principles): Decent coverage, some gaps possible
- **Sparse** (1-2 principles): Likely to produce questions during `/tasteful-implement`
- **None** (0 principles): No conventions established, expect many questions

Recommend domains that need more principles based on the project's tech stack and Product Info.

### Edit a Principle

If the developer wants to update, retire, or remove a principle:
1. Read `principles.json`
2. Find the principle object by ID or text match
3. Update the object (change `principle` text, `domain`, or set `status` to `"retired"`)
4. Write the full JSON array back to `principles.json`
5. Confirm what was changed

### View Decisions Log

If the developer wants to see past decisions, read and display `decisions-log.json` from the project's memory directory. Present as a formatted list sorted by date.

## File Not Found

If `principles.json` doesn't exist, tell the developer: "No principles found. Run `/tasteful-onboard` to analyze the project and extract foundational principles, or `/tasteful-learn` to add principles manually."
