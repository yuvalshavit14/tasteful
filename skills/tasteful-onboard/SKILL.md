---
name: tasteful-onboard
description: "Deep project onboarding — analyze codebase, create product info document, extract foundational principles. Use when setting up a new project, running initial analysis, or when user says 'onboard', 'tasteful onboard', or 'analyze this project'."
---

# Project Onboarding

You are initializing this project for AI-assisted development. Your job is to deeply understand this codebase and create foundational documents that will guide all future development:
1. A **Product Info** document (`product-info.md`) — comprehensive product context (markdown)
2. A **Principles** file (`principles.json`) — actionable conventions extracted from the codebase (JSON)
3. A **Decisions Log** (`decisions-log.json`) — record of product/technical decisions (JSON)
4. A **UI Flows** file (`ui-flows.json`) — routes, auth requirements, data dependencies (JSON)
5. A **Test Config** file (`test-config.json`) — testing tools, environment setup, and test overlay configuration (JSON)

These documents are stored in the project's memory directory and read by all future implementation work (`/tasteful-implement`). The UI dashboard also reads them for display. Take your time. Read thoroughly. The quality of these documents directly affects every future coding session.

## Phase 0: Prerequisites

Before starting the analysis, ensure all dependencies are installed and configured. Run these checks and fix anything missing:

### 1. Agent Teams setting

Read `~/.claude/settings.json`. Check if `env.CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS` is set to `"1"`. If not, read the file, add the key (merging with any existing `env` object), and write it back. This enables team mode for `/tasteful-implement`.

### 2. Superpowers plugin

Check if superpowers is installed by looking for any directory under `~/.claude/plugins/cache/` that contains a `superpowers/` subdirectory (e.g., `~/.claude/plugins/cache/superpowers-dev/superpowers/` or `~/.claude/plugins/cache/claude-plugins-official/superpowers/`).

If not found, tell the developer:
"Superpowers plugin is required but not installed. Please run `/plugin install superpowers@claude-plugins-official` and then re-run `/tasteful-onboard`."
Stop here — do not continue onboarding without superpowers.

### 3. Vercel Agent Skills

Check if the Vercel agent skills are installed by looking for `vercel-react-best-practices/SKILL.md` in:
- `.agents/skills/` in the current project directory
- `~/.claude/skills/`

If not found, install them into the project:
```bash
npx -y skills add vercel-labs/agent-skills -y
```

If the install fails (e.g., no network), warn the developer but continue onboarding — these skills improve code quality during `/tasteful-implement` but are not strictly required for onboarding itself.

### 4. Confirm readiness

After all checks pass, briefly tell the developer what was set up (e.g., "Agent teams enabled, superpowers found, Vercel skills installed. Starting project analysis.") and proceed to Phase 1.

## Phase 1: Explore the Codebase

Launch **five parallel Explore agents** using the Task tool in a single message. All five must run concurrently.

### Agent 1: Project Overview & Configuration
```
subagent_type: "Explore"
description: "Exploring project overview and configuration"
```
Prompt the agent to find and summarize:
- README, CLAUDE.md, AGENTS.md, CONTRIBUTING.md, or any project documentation
- package.json (or equivalent) — name, scripts, all dependencies split into core framework, UI, data, AI/ML, dev tooling
- Configuration files: tsconfig, build config, tailwind.config, eslint, prettier, docker, CI/CD
- Environment variables: .env.example, .env.local.example, or references in code
- Deployment setup: Dockerfile, vercel.json, fly.toml, or similar
- Any docs/ or documentation directory

### Agent 2: Application Structure & User Flows
```
subagent_type: "Explore"
description: "Exploring application structure and user flows"
```
Prompt the agent to map out:
- Top-level directory structure and what each directory contains
- All page routes (for Next.js: every page.tsx/route.ts under app/; for other frameworks: equivalent routing)
- Navigation structure: layout files, sidebars, headers, nav components
- For each user-facing flow: entry point, key steps, outcome
- Authentication flow: sign up, sign in, protected routes
- Any middleware, guards, or redirects
- For each route: auth requirements (look for useAuth, AuthGuard, createClient + getUser, getSession, requireAuth, cookies().get, getCurrentUser, useSession, or middleware-based auth checks in the page or its layout)
- What data/state each page expects (localStorage, URL params, context, database queries)
- Navigation relationships (where does each page link to, what triggers navigation)

### Agent 3: UI, Design System & Components
```
subagent_type: "Explore"
description: "Exploring UI design system and components"
```
Prompt the agent to document:
- Global styles (globals.css, theme files): CSS variables, color palette, typography scale
- Tailwind config: check for both `tailwind.config.ts` (Tailwind v3) and CSS-based `@theme` directives in `globals.css` (Tailwind v4). Note the version — v4 uses CSS-first configuration with no JS config file.
- Component library: list all components under ui/ or components/, note which UI library (Radix, shadcn, Material, etc.)
- Layout components: how pages are structured, responsive patterns
- Animation/motion: which library (Framer Motion, CSS, etc.), philosophy (subtle vs expressive)
- Iconography: icon library, custom SVGs
- Read at least 3-4 representative page components to understand patterns

### Agent 4: Data Model & API Layer
```
subagent_type: "Explore"
description: "Exploring data model and API layer"
```
Prompt the agent to map:
- Database schema: Prisma schema, SQL migrations, Supabase types, or equivalent — all models, fields, relations
- ORM/database client setup and configuration
- Authentication/authorization: provider, session handling, RLS policies
- All API routes: method, path, purpose, request/response shape
- Data fetching patterns: server components, SWR, React Query, fetch calls
- External service integrations: AI APIs, payment, email, storage, etc.
- Any background jobs, queues, or async processing

### Agent 5: Testing Infrastructure & Local Environment
```
subagent_type: "Explore"
description: "Exploring testing infrastructure and local environment"
```
Prompt the agent to discover:
- Installed test frameworks: check package.json for Playwright, Jest, Vitest, Cypress, Testing Library, or equivalents
- Test scripts in package.json (test, test:e2e, test:unit, etc.)
- Test directories and test file patterns (*.test.ts, *.spec.ts, __tests__/, e2e/, etc.)
- Existing test configuration files: playwright.config.ts, jest.config.ts, vitest.config.ts, etc.
- Database type and access: connection strings in .env*, Prisma schema, migration scripts, seed scripts
- Authentication mechanism: how login works (session, JWT, OAuth), cookie/token names, session storage
- Existing test endpoints or test utilities: routes under /api/test/, test helpers, fixtures, factories
- Dev server setup: how to start the local environment (npm run dev, docker-compose, etc.), which port, any prerequisite services
- External services that need mocking or test mode: payment providers (Stripe test keys), email (Mailhog, Mailtrap), file storage (local vs S3), etc.
- CI/CD test pipeline configuration if any

### After agents return

Read the results from all five agents. Then **personally read 3-5 additional files** to verify the findings and fill gaps — especially files that span multiple areas (like the main layout, a representative full page, or the primary data flow from UI to API to database).

## Phase 2: Draft Product Info

Synthesize everything into a Product Info document. Cover ALL of the following sections:

### Product Overview
- Product name
- One-paragraph description: what it does, in plain language
- Core value proposition: why would someone choose this over alternatives?
- Current stage: MVP? Growth? Mature?

### Target Users
- Primary persona: who is the main user?
- Secondary personas (if any)
- Their needs: what problem does this solve for them?
- Their context: when/where/how do they use this?
- Technical sophistication: are they tech-savvy or not?

### Core User Flows
For each major flow in the app:
- Flow name
- Entry point (how does the user get here?)
- Key steps (what does the user do?)
- Outcome (what's the result?)
- Emotional arc (how should the user feel at each stage?)

### UX Principles
Based on the patterns you see in the code:
- Speed vs. polish — which is prioritized?
- Simplicity vs. power — is the UI minimal or feature-rich?
- Guidance vs. freedom — is the experience guided or open-ended?
- Consistency patterns — what's the established interaction language?

### Design Language
- **Color palette**: Primary, secondary, accent colors. When is each used?
- **Typography**: Heading font, body font. Size hierarchy.
- **Spacing**: Tight or generous? Consistent scale?
- **Component patterns**: What UI library? Custom components? Card-based? List-based?
- **Animation/motion**: Library? Philosophy — subtle or expressive?
- **Responsive approach**: Mobile-first? Desktop-first? Breakpoints?
- **Iconography**: Icon set? Custom icons?

### Tech Stack
- Framework, language, styling approach
- Database, ORM, authentication
- Hosting/deployment
- Key libraries and what they're used for
- AI/ML integrations (if any)
- Testing setup (if any)

### Current Product State
- What's fully built and working?
- What's partially built or in progress?
- What's planned but not started?
- Any visible tech debt or known issues?

### Constraints
- Platform targets (mobile, desktop, tablet, all)
- Browser support requirements
- Accessibility approach
- Performance requirements
- Regulatory/compliance needs

## Phase 3: Identify What You Don't Know

List things you could NOT determine from the code alone. Common gaps:
- Business model / monetization (how does this make money?)
- User research insights (what have they learned from real users?)
- Competitive landscape (what alternatives exist? how is this different?)
- Emotional goals (beyond utility — how should users FEEL?)
- Roadmap priorities (what's most important to build next and why?)
- Design philosophy decisions (the "why" behind major UX choices not obvious from code)
- Success metrics (what does "working" look like? what are they measuring?)

## Phase 4: Ask the Developer

Use `AskUserQuestion` to ask the developer ONLY questions you could NOT answer from the code. Structure them as focused, grouped questions.

**Good questions:**
- "I see Stripe integration and a payment page, but can't determine the pricing model. Is it per-item purchase, subscription, or freemium?"
- "The app is mobile-responsive but I can't tell if mobile is the primary target. Is this mobile-first or desktop-first?"

**Bad questions (you should already know from the code):**
- "Who are your users?" (the code tells you)
- "What tech stack do you use?" (you read package.json)

Group related questions into 2-4 `AskUserQuestion` calls, each with 1-4 questions. Use the `options` field to provide multiple-choice answers where possible — this is easier for the developer to answer than open-ended questions.

**Testing-specific questions to ask** (based on what Agent 5 could NOT determine from the code):
- What testing tools or test infrastructure exists that the agent might not discover automatically? (custom test endpoints, internal tools, seed scripts not in the repo, etc.)
- Are there any external services that need special test-mode configuration? (Stripe test keys, email catching, mock APIs, etc.)
- Is there a preferred way to bypass authentication for testing? (test user credentials, auth bypass endpoint, service account, etc.)
- Any specific test data or scenarios that are important to support? (edge cases, specific user states, demo data sets, etc.)

Only ask testing questions that Agent 5 could NOT answer from the code. If the project has comprehensive test infrastructure already documented, skip redundant questions.

Wait for all responses before proceeding to Phase 5.

## Phase 5: Finalize and Save

1. Incorporate the developer's answers into your Product Info document
2. Review it one final time for completeness and accuracy
3. **Save the Product Info** by writing the complete markdown to the project's memory directory:
   - Use the Write tool to save to the memory directory for this project
   - The file should be named `product-info.md`

4. **Extract 5-10 foundational principles** from the codebase. These should be specific, actionable conventions — not generic advice.

   Write all principles to `principles.json` in the same memory directory as a JSON array:

   ```json
   [
     {
       "id": "p-001",
       "domain": "Styling",
       "principle": "Use HSL color variables from globals.css — never use raw hex/rgb in components",
       "source": "onboarding",
       "status": "active",
       "createdAt": "2026-02-11"
     },
     {
       "id": "p-002",
       "domain": "Architecture",
       "principle": "Server Components by default, Client Components only when hooks or interactivity are needed",
       "source": "onboarding",
       "status": "active",
       "createdAt": "2026-02-11"
     }
   ]
   ```

   Generate sequential IDs (`p-001`, `p-002`, ...). Use today's date for `createdAt`. Set `status` to `"active"`.

   Example principles:
   - [Styling] "Use HSL color variables from globals.css — never use raw hex/rgb in components"
   - [Architecture] "Server Components by default, Client Components only when hooks or interactivity are needed"
   - [UX] "Mobile-first responsive design using Tailwind breakpoints (sm, md, lg)"
   - [Animation] "Use Framer Motion for all page transitions and micro-interactions"
   - [Data] "All form submissions should show immediate visual feedback via optimistic UI"
   - [API] "Use Server Actions for mutations, route handlers only for streaming or external APIs"

5. **Initialize the decisions log** by writing `decisions-log.json` as an empty JSON array:

   ```json
   []
   ```

6. **Create the UI Flows document** by writing `ui-flows.json` to the memory directory.

   Use Agent 2's route mapping to build this. Group routes by user flow (not alphabetically). For each route, document auth requirements, data dependencies, and navigation:

   ```json
   [
     {
       "flow": "Story Creation",
       "routes": [
         {
           "path": "/create/select-gender",
           "auth": "none",
           "description": "Gender/pronoun selection for the child character",
           "data": "StoryContext (localStorage)",
           "navigatesTo": ["/create/select-age"]
         },
         {
           "path": "/create/select-age",
           "auth": "none",
           "description": "Age selection",
           "data": "StoryContext (localStorage) — requires childName, pronoun",
           "navigatesTo": ["/create/select-personality"]
         }
       ]
     }
   ]
   ```

7. **Create the Test Config** by writing `test-config.json` to the memory directory.

   Use Agent 5's testing discovery and the developer's answers to build this. Document everything an agent needs to test the project end-to-end:

   ```json
   {
     "generic_tools": ["playwright-cli"],
     "environment": {
       "start_command": "npm run dev",
       "port": 3000,
       "ready_check": "http://localhost:3000",
       "prerequisite_commands": []
     },
     "overlay_branch": "tasteful/test-overlay",
     "overlay_paths": ["app/api/__test__/"],
     "project_specific_tools": [
       {
         "name": "auth-bypass",
         "type": "endpoint",
         "description": "POST /api/__test__/auth-bypass creates a test session without login",
         "usage": "curl -X POST http://localhost:3000/api/__test__/auth-bypass -c cookies.txt"
       },
       {
         "name": "seed-data",
         "type": "endpoint",
         "description": "POST /api/__test__/seed creates default test data",
         "usage": "curl -X POST http://localhost:3000/api/__test__/seed"
       },
       {
         "name": "reset-state",
         "type": "endpoint",
         "description": "POST /api/__test__/reset clears all test data and returns to clean state",
         "usage": "curl -X POST http://localhost:3000/api/__test__/reset"
       }
     ],
     "database": {
       "type": "sqlite",
       "connection": "prisma/dev.db",
       "reset_command": "npx prisma db push --force-reset && npx prisma db seed"
     },
     "external_services": []
   }
   ```

   Adapt the structure to match what Agent 5 discovered. Omit sections that don't apply (e.g., if there's no database, omit the `database` field). Add any project-specific tools the developer mentioned. The `project_specific_tools` array should include every tool needed for E2E testing — both discovered existing tools and tools that will be created on the overlay branch.

8. **Create the Test Overlay Branch.**

   This is a dedicated git branch containing project-specific test infrastructure (auth bypass endpoints, data seeding endpoints, state reset endpoints) that agents apply to their worktrees during E2E testing. The overlay keeps test code out of the main branch.

   **Process:**
   a. Create a new branch from main: `git checkout -b tasteful/test-overlay`
   b. Implement the project-specific test tools listed in `test-config.json`:
      - **Auth bypass endpoint** — creates an authenticated session without requiring login. The implementation depends on the project's auth mechanism (e.g., set a session cookie, create a JWT, insert a session record).
      - **Data seeding endpoint** — creates realistic test data for the main user flows. Read the UI flows and product info to determine what data is needed.
      - **State reset endpoint** — clears test data and returns the environment to a clean state. Should be safe to call repeatedly.
      - **Any other project-specific tools** identified by Agent 5 or the developer.
   c. Place all test endpoints under a dedicated directory (e.g., `app/api/__test__/` for Next.js) that can be cleanly overlaid.
   d. Commit all test infrastructure to the overlay branch.
   e. Switch back to the original branch: `git checkout main`
   f. Confirm the overlay branch name matches what's in `test-config.json`.

   **Important:** The overlay branch is NOT merged to main. It exists as a source of test files that are copied into engineer worktrees during the E2E testing phase of `/tasteful-implement`. The copied files will appear as untracked in the worktree — this is expected and they are cleaned up after testing.

9. Confirm completion to the developer with a summary of what was created (product-info.md, principles.json, decisions-log.json, ui-flows.json, test-config.json, and the tasteful/test-overlay branch).

## Important Notes

- If memory files already exist (re-onboarding), read them first and ask the developer if they want to update or replace.
- These files are the PM agent's brain. In `/tasteful-implement` team mode, the PM agent reads product-info.md, principles.json, and decisions-log.json to answer product questions from engineers — so the developer is only interrupted for genuinely novel decisions. The richer and more accurate these files are, the fewer interruptions the developer gets. They are kept current via `/tasteful-sync`. The tasteful dashboard UI also reads these files for display.
- The test-config.json and test overlay branch are used by `/tasteful-implement` during the E2E testing phase. Engineers apply the overlay to their worktrees, use the tools described in test-config.json, and test against acceptance criteria from brainstorming. A thorough testing setup during onboarding prevents engineers from getting stuck during testing.
- Quality matters more than speed. A thorough onboarding saves hours of future questions.
