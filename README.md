# tasteful

AI-assisted development with product context. A Claude Code plugin that helps you onboard projects, build features with knowledge-aware agents, and grow a living knowledge base.

## What it does

Tasteful gives Claude Code a memory. Instead of treating every coding session as a blank slate, tasteful builds and maintains a knowledge base about your project — product context, design conventions, past decisions — so AI agents make consistent, informed choices.

**The workflow:**

1. **Onboard** your project once — tasteful analyzes your codebase and creates foundational documents
2. **Build features** with agents that know your product, follow your conventions, and only ask you about genuinely novel decisions
3. **Grow the knowledge base** over time — every decision gets logged, patterns become principles, and the system gets smarter

## Skills

### Tasteful workflow

| Skill | Command | Description |
|-------|---------|-------------|
| **Onboard** | `/tasteful-onboard` | Deep project analysis. Creates product info, principles, decisions log, UI flows, and test infrastructure |
| **Implement** | `/tasteful-implement` | Build features with full product context. Single feature or parallel team mode with a PM agent |
| **Breakdown** | `/tasteful-breakdown` | Decompose a high-level goal into independent, implementable features |
| **Learn** | `/tasteful-learn` | Add or update principles in the knowledge base |
| **Principles** | `/tasteful-principles` | View, search, and manage the project knowledge base |
| **Sync** | `/tasteful-sync` | Reconcile the knowledge base with recent code changes |

### Bundled engineering skills

These skills are used by tasteful agents (PM and engineers) during implementation for code quality and design decisions:

| Skill | Description |
|-------|-------------|
| **vercel-react-best-practices** | 57 rules for React/Next.js performance — eliminating waterfalls, bundle optimization, server-side performance, re-render optimization |
| **vercel-composition-patterns** | Component architecture patterns — compound components, explicit variants, state management, React 19 patterns |
| **web-design-guidelines** | Web UI best practices — accessibility, responsive design, interaction patterns |

## Installation

Requires [Claude Code](https://claude.ai/code).

```bash
# Add the tasteful marketplace
/plugin marketplace add yuvalshavit14/tasteful

# Install the plugin
/plugin install tasteful@tasteful
```

## Dependency

`/tasteful-implement` requires the [superpowers](https://github.com/obra/superpowers) plugin for its engineering workflow (git worktrees, brainstorming, planning, subagent-driven development, TDD, verification).

```bash
/plugin install superpowers@claude-plugins-official
```

## Getting started

After installation, open any project and run:

```
/tasteful-onboard
```

This takes a few minutes. Tasteful will:
- Explore your codebase with parallel agents
- Ask you a few questions it can't answer from code alone
- Create a knowledge base in your project's memory directory

Then build features with:

```
/tasteful-implement add user settings page
```

The agent will brainstorm the design (checking your knowledge base before asking you), plan, build, E2E test, and log all decisions for next time.

## How it works

### Knowledge base

Tasteful maintains these files in Claude Code's project memory directory:

| File | Purpose |
|------|---------|
| `product-info.md` | Product context — what it does, who uses it, design language, tech stack |
| `principles.json` | Actionable conventions — coding patterns, UX rules, architecture decisions |
| `decisions-log.json` | Record of every product/technical decision with rationale |
| `ui-flows.json` | Route map with auth requirements, data dependencies, navigation |
| `test-config.json` | Testing tools, environment setup, test overlay configuration |

### PM agent (team mode)

When building multiple features in parallel, `/tasteful-implement` spawns a PM agent that:
- Answers product/UX questions from engineers using the knowledge base
- Only escalates to you when it genuinely can't find an answer
- Logs every decision so the knowledge base grows

### Test overlay

During onboarding, tasteful creates a dedicated git branch (`tasteful/test-overlay`) with project-specific test infrastructure (auth bypass, data seeding, state reset endpoints). During E2E testing, these files are overlaid into engineer worktrees without polluting the main branch.

## License

MIT
