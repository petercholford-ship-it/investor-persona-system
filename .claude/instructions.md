# Claude Code Working Instructions - Multi-Persona AI System

**Read this at the start of every session.**

## Communication Style

**Always recommend the objectively best solution.**
- Don't default to middle-ground options to be "polite" or "non-confrontational"
- We're equals working together to find the best answer
- Be direct - challenge my assumptions when they're wrong
- If option A is clearly better than B and C, say so and explain why

**When presenting options:**
- Start with your actual recommendation and reasoning
- Don't present 3 options just to appear balanced
- If you're uncertain, say "I'm uncertain between A and B because..." not "here are 3 options"

**Communication:**
- Concise and direct
- No preamble or postamble unless it adds value
- If you made a mistake, own it directly - don't deflect

---

## Core Principles

**1. Research Before Reacting**

In the face of complexity, don't react - research first.
- Read BUILD_GUIDE.md: docs/investor-persona-build-guide.md (start here for context)
- Check existing templates before creating new patterns
- Understand the current phase before implementing
- Read existing code to understand patterns before proposing changes

**Required Reading Order:**
1. docs/persona-system-build-guide.md - Full architecture and build plan
2. templates/PERSONA_TEMPLATE.md - How to define personas
3. templates/PHASE_*.md - Current phase requirements
4. README.md - Project overview and branch structure

**2. Simplicity First (Automated Build)**

This project is designed for maximum automation via Claude Code.

Ask before writing code:
- What's the simplest version that proves it works?
- Does this match the build guide specification?
- Can this be tested immediately?
- Does this complete a checkbox in the build guide?

**Red flags (STOP and ask user):**
- Deviating from build guide without reason
- Adding features not in current phase
- More than 3 files changed for single checkbox
- Can't map change back to specific build guide step

**3. Falsifiable Hypotheses**

Before fixing ANY problem:
1. State clear hypothesis: "The problem is X because Y"
2. Write the test FIRST: How will I know if hypothesis is correct?
3. Test WITHOUT changing code: Check logs, database, API responses
4. If wrong: Document what I learned, don't jump to next hypothesis
5. Only write code if hypothesis is confirmed by evidence

Example:
- ❌ BAD: "Supabase connection failing, probably wrong URL, let me fix config"
- ✅ GOOD: "Hypothesis: Supabase URL in .env is malformed. Test: Log the URL and check format against Supabase docs."

**4. Build Guide is Source of Truth**

This is greenfield development with a detailed build guide.

Before implementing:
1. Read the specific day/phase in build guide
2. Follow the "What Claude Code will create" specification exactly
3. Complete the "Your steps" checklist
4. Mark verification checklist items
5. Update progress in phase template

**User has granted blanket "yes" for build guide implementation.**
- If it's in the build guide, implement it without asking
- If it's NOT in the build guide, ask before implementing
- If build guide is unclear, ask for clarification

---

## Technical Decision-Making

**Always choose:**
- Most automated over manual
- Earliest error detection over later detection
- Fastest feedback loops
- Enforcement over optional checks
- Prevention over detection

**Stack Decisions (from Build Guide):**
- Frontend: Next.js 14 with App Router
- Backend: Next.js API Routes
- Database: Supabase (PostgreSQL + Auth + pgvector)
- Cache: Upstash Redis
- LLM: Claude 3.5 Sonnet (Anthropic)
- Deployment: Vercel

**Don't deviate from stack without explicit user approval.**

---

## Workflow

### Pre-Implementation Checks

Before starting any build step:
```bash
# Check which phase we're in
cat templates/PHASE_*_STATUS.md

# Read the day's requirements
grep -A 20 "Day X:" docs/investor-persona-build-guide.md
```

### Implementation Flow

1. **Read build guide section** for current day/task
2. **Create files** as specified in "What Claude Code will create"
3. **Test immediately** - don't batch multiple days
4. **Update status template** - mark checkboxes complete
5. **Verify** - run verification checklist from build guide
6. **Commit** - one commit per day/major checkpoint

### Testing

- Test each feature immediately after implementation
- Don't move to next day until current day verified
- Run builds before committing: `npm run build`
- Test API endpoints with curl/Postman
- Verify database changes in Supabase dashboard

### Documentation

**Phase Status Tracking:**
When completing build steps, update templates/PHASE_*_STATUS.md:
- Mark checkboxes complete: `- [ ]` → `- [x]`
- Add notes on any deviations or issues
- Record completion dates
- Link to relevant commits

**No new .md files** without approval - use templates.

---

## Standing Permissions (EVERGREEN)

**User has provided ongoing consent - NEVER ask permission for:**

✅ **WebFetch** - Use without asking for:
- https://investor-persona-*.vercel.app (preview deployments)
- Production URL once deployed
- Any investor-persona-system domains

✅ **WebSearch** - Use without asking for:
- Architecture best practices research
- Next.js / Supabase documentation
- Claude API documentation
- Technology comparisons for stack decisions

✅ **Safe Bash commands:**
- `npm install`, `npm run build`, `npm run dev`
- `yes | [safe command]` for automation
- Git operations (add, commit, push)
- curl for API testing
- Database commands via Supabase CLI

✅ **Blanket "Yes"** - User has granted:
- Implement anything in build guide without asking
- Deploy to Vercel preview environments
- Create database migrations matching build guide schema
- Install dependencies needed for build guide stack

---

## Build Progress Tracking

**Current Phase:** Update in templates/CURRENT_PHASE.md

**Checkpoint commits:**
- After each day: `git commit -m "feat: Complete Day X - [feature name]"`
- After each phase: `git commit -m "feat: Complete Phase X - [phase name]"`
- After verification: `git commit -m "test: Verify [feature] working"`

**Status updates:**
- Update templates/PHASE_*_STATUS.md after each day
- Mark verification checklists complete
- Document any deviations or learnings
- Track actual time vs estimated time

---

## Automation Philosophy

**This project prioritizes automation:**
- Claude Code writes all code (user reviews/tests)
- Deployments are automatic (Vercel + GitHub)
- Tests should be automated (not manual)
- Database migrations auto-applied (Supabase)
- Type checking enforced (TypeScript strict mode)

**User's role:**
- Product owner (define requirements)
- Reviewer (approve designs, test features)
- Decision maker (choose between options)
- Configuration (add API keys, env vars)

**Claude Code's role:**
- Write all code following build guide
- Test implementations
- Document changes
- Fix bugs
- Suggest improvements

---

## Quick Reference: Key Files

**Start Every Session:**
1. Read this file (.claude/instructions.md)
2. Check templates/CURRENT_PHASE.md - know where we are
3. Read relevant phase in docs/investor-persona-build-guide.md

**Before Implementation:**
- docs/investor-persona-build-guide.md - Detailed day-by-day plan
- templates/PHASE_*_STATUS.md - Current progress tracking

**After Implementation:**
- Update templates/PHASE_*_STATUS.md - mark complete
- Update templates/CURRENT_PHASE.md - note progress
- Commit with descriptive message

---

## Project-Specific Conventions

**File Structure:**
```
/app                    - Next.js 14 App Router (frontend + API)
  /api                  - API routes
  /(auth)               - Auth pages
  /(dashboard)          - Protected dashboard
  /chat/[id]            - Chat interface
  /admin                - Admin panel
/components             - React components
/lib                    - Shared utilities
  /claude               - Claude API integration
  /supabase             - Supabase client
  /memory               - RAG/memory system
  /quota                - Usage tracking
/types                  - TypeScript types
/supabase               - Database migrations
  /migrations           - SQL migration files
  /seed                 - Seed data (personas)
/docs                   - Documentation
/templates              - Build tracking templates
```

**Naming Conventions:**
- Components: PascalCase (e.g., `ChatMessage.tsx`)
- Utilities: camelCase (e.g., `tokenCounter.ts`)
- API routes: kebab-case folders (e.g., `/api/chat/route.ts`)
- Database: snake_case (e.g., `user_personas`)
- Environment variables: SCREAMING_SNAKE_CASE (e.g., `CLAUDE_API_KEY`)

**Commit Message Format:**
```
<type>: <description>

[optional body]

Examples:
feat: Add streaming chat interface (Day 5)
fix: Correct token counting for embeddings
docs: Update Phase 1 status - Days 1-4 complete
test: Verify RAG memory retrieval working
```

Types: `feat`, `fix`, `docs`, `test`, `refactor`, `chore`

---

**Version:** 1.0 (Multi-Persona AI System - Generic Infrastructure)
**Updated:** 2025-10-26
**Status:** Active - Read at start of every session

**Build Guide Location:** docs/persona-system-build-guide.md
**Persona Template:** templates/PERSONA_TEMPLATE.md
**Current Phase:** Check templates/CURRENT_PHASE.md

**Branch Structure:**
- `main`: Generic infrastructure (you are here)
- `investor-personas`: Example implementation with investor personas
- Create your own branch for your domain personas
