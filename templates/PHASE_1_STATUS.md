# Phase 1: Foundation (Week 1) - Status

**Duration:** Days 1-4
**Status:** Not Started
**Started:** N/A
**Completed:** N/A

---

## Overview

Phase 1 establishes the project foundation: Next.js setup, Supabase database, authentication, and investor persona configuration.

---

## Day 1: Project Setup

**Status:** Not Started
**Estimated Time:** 2-3 hours
**Actual Time:** N/A

### Tasks

- [ ] Create Next.js 14 project with TypeScript
- [ ] Configure Tailwind CSS
- [ ] Set up App Router structure
- [ ] Configure environment variables structure
- [ ] Set up `.env.example` file

### Claude Code Creates

- [ ] `/app` directory (Next.js 14 App Router)
- [ ] `/components` directory
- [ ] `tailwind.config.ts`
- [ ] `.env.example` file
- [ ] `tsconfig.json`

### Your Checklist

- [ ] Run `npm install` to install dependencies
- [ ] Create `.env.local` and add API keys
- [ ] Run `npm run dev` to verify it works
- [ ] Visit `http://localhost:3000`

### Verification

- [ ] Project runs without errors
- [ ] Tailwind CSS working
- [ ] TypeScript compiling
- [ ] Environment variables loading

### Notes

<!-- Add any notes, issues, or deviations here -->

---

## Day 2: Supabase Setup

**Status:** Not Started
**Estimated Time:** 2-3 hours
**Actual Time:** N/A

### Tasks

- [ ] Design database schema (users, personas, conversations, messages, memories)
- [ ] Create SQL migration with pgvector support
- [ ] Set up Row Level Security (RLS) policies
- [ ] Generate TypeScript types from schema

### Claude Code Creates

- [ ] `/supabase/migrations/001_initial_schema.sql`
- [ ] Database schema with RLS policies
- [ ] `/types/database.ts` (TypeScript types)

### Your Checklist

- [ ] Create Supabase project at supabase.com
- [ ] Run migration in Supabase SQL Editor
- [ ] Enable pgvector extension: `CREATE EXTENSION vector;`
- [ ] Copy connection string to `.env.local`
- [ ] Verify tables created in Supabase dashboard

### Verification

- [ ] All 5 tables created (users, personas, conversations, messages, conversation_memories)
- [ ] RLS policies enabled (green checkmarks in Supabase)
- [ ] pgvector extension installed
- [ ] Connection string works

### Schema Checklist

**Tables Created:**
- [ ] `personas` - Investor persona definitions
- [ ] `conversations` - Conversation sessions
- [ ] `messages` - Individual messages
- [ ] `conversation_memories` - RAG vectors with pgvector
- [ ] `users` - User accounts and tiers (via Supabase Auth)

**RLS Policies:**
- [ ] Users can only see their own conversations
- [ ] Users can only access personas they're entitled to
- [ ] Admin users can see all data

### Notes

<!-- Database connection details, any schema modifications -->

---

## Day 3: Authentication

**Status:** Not Started
**Estimated Time:** 3-4 hours
**Actual Time:** N/A

### Tasks

- [ ] Implement Supabase Auth
- [ ] Create login page with email/password
- [ ] Add protected routes middleware
- [ ] Create user context provider for auth state
- [ ] Set up logout functionality

### Claude Code Creates

- [ ] `/app/login/page.tsx` (login UI)
- [ ] `/lib/supabase.ts` (Supabase client)
- [ ] `/middleware.ts` (route protection)
- [ ] `/contexts/AuthContext.tsx` (auth state)
- [ ] `/app/api/auth/logout/route.ts` (logout endpoint)

### Your Checklist

- [ ] In Supabase dashboard → Authentication → Settings
- [ ] Disable email confirmations (for testing)
- [ ] Add yourself as admin user manually in Supabase
- [ ] Set `is_admin = true` in users table for your account
- [ ] Test login at `/login`

### Verification

- [ ] Can create account
- [ ] Can log in with email/password
- [ ] Redirects to dashboard after login
- [ ] Can't access `/dashboard` without login
- [ ] Logout works correctly
- [ ] Protected routes redirect to `/login`

### Notes

<!-- Auth configuration, test user credentials -->

---

## Day 4: Investor Persona Configuration

**Status:** Not Started
**Estimated Time:** 2-3 hours
**Actual Time:** N/A

### Tasks

- [ ] Create seed data for 4 investor personas
- [ ] Implement admin interface to view/edit personas
- [ ] Create persona prompt templates
- [ ] Set up persona access control (by tier)

### Claude Code Creates

- [ ] `/supabase/seed/personas.sql` (initial personas)
- [ ] `/app/admin/personas/page.tsx` (admin UI)
- [ ] `/lib/personas/investor-prompts.ts` (prompt templates)
- [ ] `/app/api/admin/personas/route.ts` (CRUD API)

### Your Checklist

- [ ] Run seed SQL in Supabase SQL Editor
- [ ] Access `/admin/personas` (as admin user)
- [ ] Review the 4 default personas
- [ ] Customize if needed

### Verification

- [ ] 4 personas visible in admin panel:
  - [ ] VC Partner (Morgan)
  - [ ] Angel Investor (Alex)
  - [ ] Hedge Fund Analyst (Jordan)
  - [ ] PE Associate (Taylor)
- [ ] Each has complete system prompt (500+ tokens)
- [ ] Can edit and save changes
- [ ] Tier restrictions work (free users see limited personas)

### Personas Checklist

- [ ] **VC Partner**: Early-stage focus, Series A/B expertise
- [ ] **Angel Investor**: Seed/pre-seed, founder-focused
- [ ] **Hedge Fund Analyst**: Public markets, quantitative
- [ ] **PE Associate**: Buyouts/growth equity, operational

### Notes

<!-- Persona customizations, which persona to prioritize for testing -->

---

## Phase 1 Summary

**Days Completed:** 0 / 4
**Overall Status:** Not Started

**Blockers:** None

**Next Phase:** Phase 2 - Core Chat (Days 5-7)

---

## Sign-Off

**Phase 1 Complete?** [ ] Yes / [ ] No

**Ready for Phase 2?** [ ] Yes / [ ] No

**Notes:**
<!-- Overall phase notes, learnings, issues to address in next phase -->
