# Investor Persona System - Build Guide for Claude Code

> **Designed for**: Product owners and technical PMs building via Claude Code assistance
> **First Persona**: Investor Assistant (VC, Hedge Fund, PE, Angel, EIS)
> **Approach**: Greenfield, API-first architecture with chat interface

---

## Table of Contents

1. [Market Precedents & Positioning](#market-precedents--positioning)
2. [Cost Breakdown & Drivers](#cost-breakdown--drivers)
3. [Memory Implementation Strategy](#memory-implementation-strategy)
4. [Recommended Architecture](#recommended-architecture)
5. [Step-by-Step Build Guide](#step-by-step-build-guide)
6. [Authentication & Permissions Strategy](#authentication--permissions-strategy)
7. [Investor Persona Specification](#investor-persona-specification)
8. [Future Roadmap](#future-roadmap)

---

## Market Precedents & Positioning

### Direct Comparators (2025)

#### **1. Character.AI** (Consumer Focus)
- **What**: Chat with AI personas (historical figures, fictional characters)
- **Users**: 200M+ users
- **Model**: Free + $9.99/month premium
- **Key Feature**: User-created personas, entertainment focus
- **Weakness**: No business/professional personas, no API, limited memory
- **Your Advantage**: Professional investor focus, permissioned access, business-grade memory

#### **2. CustomGPT.ai** (Business Focus)
- **What**: Create custom ChatGPT personas for businesses
- **Pricing**: $89-499/month
- **Key Feature**: Up to 300 personas, emotion detection, no-code creation
- **Weakness**: Generic business personas, expensive
- **Your Advantage**: Specialized investor expertise, tiered usage model

#### **3. Digital Ray** (October 2025, Ray Dalio)
- **What**: AI assistant embodying Ray Dalio's investment principles
- **Target**: Individual investors
- **Key Feature**: Decades of Bridgewater principles embedded
- **Weakness**: Single persona (Ray Dalio only), not customizable
- **Your Advantage**: Multiple investor archetypes, customizable for different investment strategies

#### **4. WarrenAI / Decile Hub** (VC Tools)
- **What**: AI-powered due diligence and deal analysis for VCs
- **Target**: Professional investors
- **Key Feature**: 135k+ securities, automated due diligence
- **Weakness**: Tool-focused, not conversational
- **Your Advantage**: Conversational interface, persona-driven insights

### Market Gap You're Filling

**There's no dedicated conversational AI platform for investor personas with:**
- ✅ Multiple investor archetypes (VC, PE, Angel, Hedge Fund)
- ✅ Permissioned, tiered access (not public free-for-all)
- ✅ Long-term memory (RAG-based, not just context window)
- ✅ API-first for future integrations
- ✅ Professional-grade security and usage controls

**Positioning Statement**:
*"The only AI persona platform built specifically for professional and sophisticated investors, offering specialized archetypes with deep domain knowledge, long-term memory, and enterprise-grade access controls."*

---

## Cost Breakdown & Drivers

### Monthly Operating Costs (Estimated)

#### **Development Phase (Months 1-2)**
| Component | Cost | Driver |
|-----------|------|--------|
| **Vercel Pro** | $20/month | Hosting Next.js app, serverless functions |
| **Supabase Pro** | $25/month | PostgreSQL database, auth, 8GB storage |
| **Claude API** | $50-200 | **PRIMARY DRIVER**: Token usage (input + output) |
| **Redis (Upstash)** | $10-20 | Session storage, rate limiting |
| **Total** | **$105-265/month** | Scales with usage |

#### **Production Phase (100 users, 50 conversations/day)**
| Component | Cost | Driver |
|-----------|------|--------|
| **Vercel Pro** | $20/month | Bandwidth and function executions |
| **Supabase Pro** | $25/month | Database queries, storage |
| **Claude API** | **$500-1500** | **PRIMARY DRIVER**: 50 convos/day × 30 days × $0.30-0.90/conversation |
| **Redis (Upstash)** | $20/month | Caching, rate limiting |
| **Vector DB (Pinecone Free)** | $0 (up to 1 pod) | Semantic memory storage |
| **Total** | **$565-1565/month** | **Claude API dominates** |

### Cost Drivers Explained

#### **1. Claude API Tokens (80-90% of total cost)**

**Pricing Model** (Claude 3.5 Sonnet, Jan 2025):
- **Input tokens**: $3 per million tokens (~$0.003 per 1k tokens)
- **Output tokens**: $15 per million tokens (~$0.015 per 1k tokens)

**Example Conversation**:
```
System Prompt (Investor Persona): 500 tokens
User Question: "Analyze this Series A term sheet..." (1,000 tokens)
Claude Response: "Here's my analysis..." (2,000 tokens)

Cost per conversation:
- Input: 1,500 tokens × $0.003 = $0.0045
- Output: 2,000 tokens × $0.015 = $0.030
- Total: $0.0345 (~3.5 cents)
```

**Daily Cost at Scale**:
- 50 conversations/day × $0.035 = $1.75/day
- Monthly: $52.50 for 50 convos/day
- **BUT**: Investor conversations are LONG (5-10k token responses)
- More realistic: $0.20-0.50 per conversation
- Monthly: **$300-750** for 50 convos/day

#### **2. Memory/RAG Costs** (10-15% of total)

**Option A: Pinecone (Vector Database)**
- Free tier: 1 pod, 100k vectors, 5GB storage
- Paid: $70/month for 1 pod (up to 100k users)

**Option B: Supabase pgvector** (Recommended for MVP)
- Included in Supabase Pro ($25/month)
- PostgreSQL extension, no separate cost
- Good for up to 100k embeddings

**Why needed?**: Long-term memory beyond context window
- Store conversation summaries as vectors
- Semantic search to retrieve relevant past context
- User preferences and insights

#### **3. Database & Infrastructure** (5-10% of total)

**Supabase**: $25/month includes:
- 8GB database storage
- 50GB bandwidth
- 500k monthly active users
- Built-in auth, RLS, real-time

**Vercel**: $20/month includes:
- Unlimited deployments
- 100GB bandwidth
- 1000 serverless function hours

### Cost Optimization Strategies

**1. Token Management**
- ✅ Trim conversation history (keep last 10 messages)
- ✅ Summarize older context (100 messages → 500 tokens)
- ✅ Use caching for repeated system prompts
- ✅ Limit output tokens (max 2000 tokens per response)

**2. Tiered Usage Model**
```
Free Tier: 10 messages/month (trial)
Starter: $29/month - 100 messages (~$30 cost → break even)
Professional: $99/month - 500 messages (~$150 cost → 34% margin)
Enterprise: $499/month - Unlimited* (~$500 cost → volume discount)
```

**3. Caching Strategy**
- Cache common investor questions/answers
- Store persona system prompts in memory (not re-sent each time)
- Use Redis for session state

---

## Memory Implementation Strategy

### The Memory Problem

**Claude's Context Window**: 200k tokens (~150k words)
- Sounds huge, but...
- 10 long investor conversations = context full
- No persistence between sessions
- Expensive to send full history every time

**What Users Expect**:
- "Remember our discussion about that Series B deal last week"
- "You analyzed my portfolio composition yesterday—has anything changed?"
- Continuous conversation across days/weeks

### Memory Solution: **Hybrid Approach**

#### **Tier 1: Short-Term Memory (Session)**
**Storage**: Redis (in-memory cache)
**Duration**: Active session + 24 hours
**Contents**:
- Last 10 messages (full text)
- Current conversation context
- User's current focus (e.g., "analyzing XYZ startup")

**Cost**: Negligible (~$10-20/month for thousands of sessions)

#### **Tier 2: Medium-Term Memory (Conversation History)**
**Storage**: PostgreSQL (Supabase)
**Duration**: 90 days (configurable)
**Contents**:
- Full conversation logs
- Structured data (companies discussed, terms analyzed)
- User preferences

**Cost**: Included in Supabase Pro

#### **Tier 3: Long-Term Memory (Semantic/RAG)**
**Storage**: Vector database (pgvector in Supabase)
**Duration**: Indefinite
**Contents**:
- Conversation summaries (vectorized)
- Key insights and decisions
- User's investment thesis
- Portfolio information

**How it works**:
1. Every 10 messages, summarize conversation
2. Generate embedding vector (384 dimensions)
3. Store in vector DB with metadata
4. On new conversation, semantic search for relevant past context
5. Inject top 3-5 relevant summaries into prompt

**Cost**: $0 (using Supabase pgvector on Pro plan)

### Technical Implementation

#### **Database Schema**

```sql
-- Conversations table (Medium-Term Memory)
CREATE TABLE conversations (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id UUID REFERENCES auth.users(id),
  persona_id UUID REFERENCES personas(id),
  created_at TIMESTAMPTZ DEFAULT NOW(),
  updated_at TIMESTAMPTZ DEFAULT NOW(),
  title TEXT, -- Auto-generated summary
  message_count INT DEFAULT 0
);

-- Messages table
CREATE TABLE messages (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  conversation_id UUID REFERENCES conversations(id),
  role TEXT CHECK (role IN ('user', 'assistant', 'system')),
  content TEXT,
  tokens_used INT,
  created_at TIMESTAMPTZ DEFAULT NOW()
);

-- Memory vectors (Long-Term Memory / RAG)
CREATE TABLE conversation_memories (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id UUID REFERENCES auth.users(id),
  conversation_id UUID REFERENCES conversations(id),
  summary TEXT, -- Human-readable summary
  embedding VECTOR(384), -- For semantic search (pgvector)
  metadata JSONB, -- {companies: [], topics: [], sentiment: ""}
  created_at TIMESTAMPTZ DEFAULT NOW()
);

-- Enable vector similarity search
CREATE INDEX ON conversation_memories USING ivfflat (embedding vector_cosine_ops);
```

#### **RAG Workflow**

```
User asks: "What did you think about that AI startup I mentioned?"

1. Generate embedding for question (OpenAI ada-002: $0.0001 per 1k tokens)
2. Query vector DB: Find top 5 similar past conversations
3. Retrieve summaries: "Week ago: Discussed AI startup 'Acme' - Series A, $10M valuation..."
4. Build context:
   - System prompt (Investor persona)
   - Retrieved memories (top 5)
   - Recent messages (last 10)
5. Send to Claude
6. Claude responds with context awareness
7. Store new summary vector for future retrieval
```

### Memory Cost Examples

**100 Active Users Scenario**:
- 10 conversations/user/month = 1,000 conversations
- 1 summary vector per 10 messages = ~5 vectors/conversation
- Total vectors/month: 5,000
- Storage: 5k vectors × 384 dims × 4 bytes = 7.68 MB
- **Cost**: $0 (within Supabase free tier for pgvector)

**Embedding Costs** (using OpenAI text-embedding-3-small):
- 5,000 summaries/month
- ~200 tokens per summary
- 1M tokens total
- **Cost**: $0.02/month (negligible)

**Retrieval Costs**:
- Vector similarity search is local DB query
- No API cost
- Fast (<50ms)

---

## Recommended Architecture

### **Tech Stack: Simple & Scalable**

```
┌─────────────────────────────────────────────────────────────┐
│                    Frontend (Next.js 14)                    │
│                                                             │
│  Routes:                                                    │
│  - /login (Auth)                                            │
│  - /dashboard (Persona selector, usage stats)               │
│  - /chat/[conversationId] (Main chat interface)             │
│  - /admin (User management, persona config) [Admin only]    │
│                                                             │
│  UI Components:                                             │
│  - ChatInterface (messages, streaming responses)            │
│  - PersonaSelector (dropdown of available personas)         │
│  - UsageIndicator (messages left this month)                │
│  - ConversationHistory (sidebar with past chats)            │
└─────────────────────────────────────────────────────────────┘
                            │
                            │ (API calls)
                            ▼
┌─────────────────────────────────────────────────────────────┐
│              Backend (Next.js API Routes)                   │
│                                                             │
│  API Endpoints:                                             │
│  POST   /api/auth/login                                     │
│  POST   /api/auth/logout                                    │
│  GET    /api/personas (list available personas)             │
│  GET    /api/conversations (user's conversation history)    │
│  POST   /api/conversations (create new conversation)        │
│  POST   /api/chat (send message, get streaming response)    │
│  GET    /api/usage (check quota: messages left)             │
│  POST   /api/admin/users (admin: manage user tiers)         │
│  POST   /api/admin/personas (admin: create/edit personas)   │
└─────────────────────────────────────────────────────────────┘
         │                      │                    │
         │                      │                    │
         ▼                      ▼                    ▼
┌─────────────────┐  ┌──────────────────┐  ┌─────────────────┐
│  Supabase       │  │  Redis           │  │  Claude API     │
│  (PostgreSQL)   │  │  (Upstash)       │  │  (Anthropic)    │
│                 │  │                  │  │                 │
│  - Auth         │  │  - Session cache │  │  - Chat         │
│  - User data    │  │  - Rate limiting │  │  - Streaming    │
│  - Conversations│  │  - Temp context  │  │                 │
│  - Personas     │  │                  │  │                 │
│  - Vectors      │  │                  │  │                 │
│   (pgvector)    │  │                  │  │                 │
└─────────────────┘  └──────────────────┘  └─────────────────┘
```

### Why This Stack?

✅ **Next.js**: Single codebase for frontend + backend
✅ **Supabase**: Auth, database, vectors all-in-one
✅ **Redis**: Fast session management
✅ **Claude API**: Best-in-class reasoning for investor queries
✅ **Vercel**: Zero-config deployment, scales automatically

### Alternative Stacks (If You Prefer Different Tools)

| Component | Recommended | Alternatives |
|-----------|-------------|--------------|
| **Frontend** | Next.js 14 | Remix, SvelteKit, Vite + React |
| **Backend** | Next.js API | Express.js, NestJS, Fastify |
| **Database** | Supabase | PlanetScale, Neon, Railway Postgres |
| **Auth** | Supabase Auth | Auth0, Clerk, Firebase Auth |
| **Vector DB** | Supabase pgvector | Pinecone, Weaviate, Qdrant |
| **Cache** | Upstash Redis | Vercel KV, Redis Cloud |
| **LLM** | Claude 3.5 Sonnet | GPT-4, Gemini Pro |
| **Hosting** | Vercel | Railway, Render, GCP Cloud Run |

---

## Step-by-Step Build Guide

> **Designed for directing Claude Code, not coding yourself**

### Prerequisites

- Node.js 18+ installed
- Git installed
- GitHub account
- Anthropic API key
- Supabase account (free)
- Upstash account (free)

### Phase 1: Foundation (Week 1)

#### **Day 1: Project Setup**

**Tell Claude Code**:
> "Create a new Next.js 14 project with TypeScript, Tailwind CSS, and the App Router. Configure it for deployment to Vercel. Set up environment variable structure for Supabase, Upstash, and Claude API keys."

**What Claude Code will create**:
- `/app` directory (Next.js 14 App Router)
- `/components` directory
- `tailwind.config.ts`
- `.env.example` file
- `tsconfig.json`

**Your checklist**:
- [ ] Run `npm install` to install dependencies
- [ ] Create `.env.local` and add your API keys
- [ ] Run `npm run dev` to verify it works
- [ ] Visit `http://localhost:3000`

---

#### **Day 2: Supabase Setup**

**Tell Claude Code**:
> "Create Supabase database schema for a multi-persona chat system. I need tables for: users, personas, conversations, messages, and conversation_memories (with pgvector for semantic search). Include Row Level Security (RLS) policies so users can only access their own data. Generate SQL migration file."

**What Claude Code will create**:
- `/supabase/migrations/001_initial_schema.sql`
- Database schema with RLS policies
- Types file (`/types/database.ts`)

**Your steps**:
1. Go to supabase.com, create new project
2. In Supabase dashboard → SQL Editor
3. Paste the migration SQL Claude generated
4. Run migration
5. Enable pgvector extension: `CREATE EXTENSION vector;`
6. Copy connection string to `.env.local`

**Verify**:
- [ ] Tables created in Supabase dashboard
- [ ] RLS policies enabled (green checkmarks)

---

#### **Day 3: Authentication**

**Tell Claude Code**:
> "Implement authentication using Supabase Auth. Create login page with email/password. Add protected routes middleware that redirects unauthenticated users to /login. Create a user context provider for auth state."

**What Claude Code will create**:
- `/app/login/page.tsx` (login UI)
- `/lib/supabase.ts` (Supabase client)
- `/middleware.ts` (route protection)
- `/contexts/AuthContext.tsx`

**Your steps**:
1. In Supabase dashboard → Authentication → Settings
2. Disable email confirmations (for testing)
3. Add yourself as admin user manually in Supabase dashboard
4. Test login at `/login`

**Verify**:
- [ ] Can create account
- [ ] Can log in
- [ ] Redirects to dashboard after login
- [ ] Can't access `/dashboard` without login

---

#### **Day 4: Investor Persona Configuration**

**Tell Claude Code**:
> "Create the investor persona specification as a database seed. I want 4 investor archetypes: VC Partner, Angel Investor, Hedge Fund Analyst, and PE Associate. Each should have a detailed system prompt with their investment philosophy, analysis framework, and communication style. Create an admin interface to view and edit these personas."

**What Claude Code will create**:
- `/supabase/seed/personas.sql` (initial personas)
- `/app/admin/personas/page.tsx` (admin UI)
- `/lib/personas/investor-prompts.ts` (prompt templates)

**Your steps**:
1. Run seed SQL in Supabase SQL Editor
2. Access `/admin/personas` (as admin user)
3. Review the 4 default personas
4. Customize if needed

**Verify**:
- [ ] 4 personas visible in admin panel
- [ ] Each has complete system prompt
- [ ] Can edit and save changes

---

### Phase 2: Core Chat (Week 2)

#### **Day 5: Chat Interface**

**Tell Claude Code**:
> "Create a chat interface with streaming responses. The UI should have: persona selector dropdown at top, conversation history sidebar, main chat area with messages, input box at bottom, and usage indicator showing messages remaining. Use a clean, professional design."

**What Claude Code will create**:
- `/app/chat/[id]/page.tsx` (chat UI)
- `/components/ChatMessage.tsx`
- `/components/ChatInput.tsx`
- `/components/PersonaSelector.tsx`
- `/components/ConversationSidebar.tsx`

**Your steps**:
1. Navigate to `/chat/new`
2. Test UI (no functionality yet)
3. Provide feedback on design to Claude
4. Iterate until satisfied

**Verify**:
- [ ] Clean, professional design
- [ ] Responsive (works on mobile)
- [ ] All components visible

---

#### **Day 6: Claude API Integration**

**Tell Claude Code**:
> "Create API route /api/chat that accepts a message, retrieves conversation history, loads the selected persona's system prompt, sends to Claude API with streaming, and stores the exchange in the database. Use Claude 3.5 Sonnet. Implement token counting and usage tracking."

**What Claude Code will create**:
- `/app/api/chat/route.ts` (streaming endpoint)
- `/lib/claude/client.ts` (Claude API wrapper)
- `/lib/claude/streaming.ts` (streaming handler)
- `/lib/usage-tracker.ts` (token counting)

**Your steps**:
1. Add Claude API key to `.env.local`
2. Test sending a message in chat UI
3. Verify streaming response appears
4. Check Supabase: message saved in database

**Verify**:
- [ ] Can send message and get response
- [ ] Response streams in real-time
- [ ] Messages saved to database
- [ ] Token count recorded

---

#### **Day 7: Memory System (RAG)**

**Tell Claude Code**:
> "Implement semantic memory using pgvector. Every 10 messages, generate a conversation summary, create an embedding using OpenAI's text-embedding-3-small, and store in conversation_memories table. When starting a conversation, retrieve the top 5 relevant past memories using vector similarity search and inject them into the Claude context."

**What Claude Code will create**:
- `/lib/memory/summarizer.ts` (conversation summarization)
- `/lib/memory/embeddings.ts` (OpenAI embedding generation)
- `/lib/memory/retrieval.ts` (vector search)
- `/app/api/chat/route.ts` (updated with memory injection)

**Your steps**:
1. Add OpenAI API key to `.env.local`
2. Have a 10+ message conversation
3. Check Supabase: `conversation_memories` table should have entries
4. Start new conversation, reference something from old conversation
5. Claude should "remember" the context

**Verify**:
- [ ] Summaries created after 10 messages
- [ ] Vectors stored in database
- [ ] Memory retrieval works in new conversations
- [ ] Claude references past context accurately

---

### Phase 3: Usage & Permissions (Week 3)

#### **Day 8: User Tiers & Quotas**

**Tell Claude Code**:
> "Implement a tiered usage system. Add a 'tier' column to users table (free, starter, professional, enterprise). Create a quota system that limits free users to 10 messages/month, starter to 100/month, professional to 500/month, and enterprise to unlimited. Show usage remaining in the UI. Block messages when quota exceeded."

**What Claude Code will create**:
- Migration: `/supabase/migrations/002_add_user_tiers.sql`
- `/lib/quota/checker.ts` (quota enforcement)
- `/app/api/usage/route.ts` (GET current usage)
- `/components/UsageIndicator.tsx` (UI display)

**Your steps**:
1. Run migration in Supabase
2. Set your user tier to "professional" in Supabase dashboard
3. Send messages and watch usage counter decrease
4. Manually set quota to 0, verify blocking works

**Verify**:
- [ ] Usage indicator shows correct count
- [ ] Quota enforcement works
- [ ] Different tiers have different limits
- [ ] Clear error message when quota exceeded

---

#### **Day 9: Admin Panel**

**Tell Claude Code**:
> "Create an admin dashboard at /admin. It should show: list of all users with their tier and usage stats, ability to change user tier, ability to reset usage quota, list of all conversations (anonymized), and system-wide usage statistics (total messages, total tokens, cost estimate). Restrict access to users with is_admin=true in database."

**What Claude Code will create**:
- `/app/admin/page.tsx` (main dashboard)
- `/app/admin/users/page.tsx` (user management)
- `/app/api/admin/users/route.ts` (user CRUD)
- `/app/api/admin/stats/route.ts` (system stats)
- Middleware update for admin-only routes

**Your steps**:
1. In Supabase, add `is_admin` column to users table
2. Set your user to `is_admin = true`
3. Access `/admin`
4. Test user tier changes
5. Review system stats

**Verify**:
- [ ] Can access admin panel
- [ ] Non-admin users get 403 error
- [ ] Can change user tiers
- [ ] Stats show accurate data

---

#### **Day 10: Rate Limiting**

**Tell Claude Code**:
> "Add rate limiting using Upstash Redis. Limit users to 10 messages per minute to prevent abuse. Show a clear error message when rate limited. Also add IP-based rate limiting for the login endpoint (5 attempts per 15 minutes) to prevent brute force attacks."

**What Claude Code will create**:
- `/lib/rate-limiter.ts` (Redis-based rate limiting)
- `/app/api/chat/route.ts` (updated with rate check)
- `/app/api/auth/login/route.ts` (login rate limiting)

**Your steps**:
1. Create Upstash Redis database (free tier)
2. Add Redis URL to `.env.local`
3. Test sending >10 messages rapidly
4. Verify rate limit error appears

**Verify**:
- [ ] Rate limiting activates after 10 messages/minute
- [ ] Clear error message displayed
- [ ] Rate limit resets after 1 minute
- [ ] Login endpoint also rate limited

---

### Phase 4: Polish & Deploy (Week 4)

#### **Day 11: Error Handling & Loading States**

**Tell Claude Code**:
> "Add comprehensive error handling throughout the app. Show user-friendly error messages for: API failures, network issues, quota exceeded, rate limiting, authentication errors. Add loading states for: message sending, conversation loading, persona switching. Add a toast notification system for success/error feedback."

**What Claude Code will create**:
- `/components/ErrorBoundary.tsx`
- `/components/Toast.tsx` (notification system)
- `/lib/error-handler.ts` (centralized error handling)
- Loading skeletons for all async components

**Your steps**:
1. Test error scenarios (disconnect internet, exceed quota, etc.)
2. Verify friendly error messages appear
3. Check loading states show during async operations

**Verify**:
- [ ] All errors have user-friendly messages
- [ ] Loading states present during async operations
- [ ] Toast notifications work
- [ ] No uncaught errors in console

---

#### **Day 12: Conversation Management**

**Tell Claude Code**:
> "Add features to manage conversations: rename conversation (auto-generate title from first message), delete conversation, archive conversation, search conversations by content, export conversation as PDF or markdown. Add conversation list to sidebar with search and filter."

**What Claude Code will create**:
- `/app/api/conversations/[id]/route.ts` (CRUD operations)
- `/components/ConversationList.tsx` (sidebar with search)
- `/lib/conversation-exporter.ts` (PDF/markdown export)
- `/components/ConversationActions.tsx` (rename/delete/archive UI)

**Your steps**:
1. Create multiple conversations
2. Test renaming, deleting, archiving
3. Test search functionality
4. Export a conversation to PDF

**Verify**:
- [ ] Can rename conversations
- [ ] Delete works (confirm dialog)
- [ ] Search finds relevant conversations
- [ ] Export produces readable PDF/markdown

---

#### **Day 13: Mobile Responsive & Accessibility**

**Tell Claude Code**:
> "Ensure the entire app is mobile responsive. The conversation sidebar should collapse into a drawer on mobile. Add keyboard shortcuts: Cmd+K to search conversations, Cmd+N for new conversation, Cmd+/ for help. Ensure all interactive elements are accessible (ARIA labels, keyboard navigation, screen reader support)."

**What Claude Code will create**:
- Updated CSS/Tailwind for mobile breakpoints
- `/components/MobileDrawer.tsx` (sidebar on mobile)
- `/lib/keyboard-shortcuts.ts`
- Accessibility improvements throughout

**Your steps**:
1. Test on mobile device or Chrome DevTools mobile view
2. Test keyboard shortcuts
3. Run accessibility audit (Chrome DevTools Lighthouse)

**Verify**:
- [ ] Works well on mobile (iPhone, Android)
- [ ] Sidebar collapses to drawer on mobile
- [ ] Keyboard shortcuts work
- [ ] Accessibility score >90% in Lighthouse

---

#### **Day 14: Documentation & Deployment**

**Tell Claude Code**:
> "Create comprehensive documentation: README.md with setup instructions, DEPLOYMENT.md for Vercel deployment steps, USER_GUIDE.md for end users, ADMIN_GUIDE.md for admin functions, API.md documenting all API endpoints. Then create Vercel deployment configuration and deploy to production."

**What Claude Code will create**:
- `/README.md`
- `/docs/DEPLOYMENT.md`
- `/docs/USER_GUIDE.md`
- `/docs/ADMIN_GUIDE.md`
- `/docs/API.md`
- `vercel.json` (deployment config)

**Your steps**:
1. Review all documentation
2. Push code to GitHub
3. Connect GitHub repo to Vercel
4. Add environment variables in Vercel dashboard
5. Deploy
6. Test production URL

**Verify**:
- [ ] Documentation is clear and complete
- [ ] Production deployment successful
- [ ] All environment variables set in Vercel
- [ ] Production app works identically to local

---

## Authentication & Permissions Strategy

### User Roles

```typescript
enum UserRole {
  ADMIN = 'admin',      // Full access, user management
  USER = 'user',        // Standard access
}

enum UserTier {
  FREE = 'free',        // 10 messages/month (trial)
  STARTER = 'starter',  // 100 messages/month
  PROFESSIONAL = 'professional', // 500 messages/month
  ENTERPRISE = 'enterprise',    // Unlimited
}
```

### Permission Matrix

| Feature | Free | Starter | Professional | Enterprise | Admin |
|---------|------|---------|--------------|------------|-------|
| **Create conversations** | ✅ | ✅ | ✅ | ✅ | ✅ |
| **Messages/month** | 10 | 100 | 500 | Unlimited | Unlimited |
| **Access investor personas** | 1 (limited) | 2 | 4 (all) | 4 (all) | All |
| **Long-term memory (RAG)** | ❌ | ✅ | ✅ | ✅ | ✅ |
| **Export conversations** | ❌ | ✅ | ✅ | ✅ | ✅ |
| **API access** | ❌ | ❌ | ❌ | ✅ | ✅ |
| **Priority support** | ❌ | ❌ | ✅ | ✅ | ✅ |
| **Custom personas** | ❌ | ❌ | ❌ | ✅ | ✅ |
| **White-label** | ❌ | ❌ | ❌ | ✅ | ✅ |
| **User management** | ❌ | ❌ | ❌ | ❌ | ✅ |

### Authentication Implications

#### **Option A: Fully Private (Invitation Only)**
**How it works**:
- Admin creates user accounts manually
- No public signup page
- Sends invitation emails with temporary passwords
- Users log in, forced to change password

**Pros**:
✅ Total control over user base
✅ No spam/abuse
✅ Easy to manage small user group (5-50 users)

**Cons**:
❌ Doesn't scale (manual user creation)
❌ No viral growth
❌ Higher support burden

**Best for**: Internal team tool, private beta, corporate deployment

---

#### **Option B: Public with Approval**
**How it works**:
- Public signup page
- Users request access (provide email, use case)
- Admin reviews and approves
- Auto-email when approved

**Pros**:
✅ Captures demand
✅ Filter out bad actors
✅ Understand user needs from requests
✅ Can grow without manual creation

**Cons**:
❌ Approval queue to manage
❌ Delay from request to access
❌ Users may forget to check email

**Best for**: Beta testing, professional tools, B2B SaaS

---

#### **Option C: Freemium (Open Signup)**
**How it works**:
- Anyone can sign up
- Default to Free tier (10 messages/month)
- Self-serve upgrade to paid tiers
- Stripe integration for payments

**Pros**:
✅ Maximum user acquisition
✅ Viral potential
✅ Self-serve (no admin overhead)
✅ Revenue from day 1

**Cons**:
❌ Abuse risk (spam, bot signups)
❌ Support burden from free users
❌ Need payment integration (Stripe)

**Best for**: Consumer products, rapid growth, established market

---

### Recommended Approach for Investor Persona

**Phase 1 (First 3 months): Option A - Invitation Only**
- Start with 10-20 hand-selected investors
- Get high-quality feedback
- Refine personas based on real usage
- Build case studies

**Phase 2 (Months 4-6): Option B - Public with Approval**
- Add signup page
- Auto-approve based on criteria (e.g., LinkedIn shows investor role)
- Grow to 100-200 users
- Establish pricing

**Phase 3 (Months 7+): Option C - Freemium**
- Open to public
- Add Stripe for self-serve payments
- Scale to 1000+ users

---

## Investor Persona Specification

### Persona 1: **VC Partner** (Early-Stage Focused)

**Character**:
- Name: "Morgan" (gender-neutral)
- Background: 15 years in venture capital, 200+ investments, focus on Series A/B
- Portfolio: Enterprise SaaS, fintech, AI/ML
- Investment thesis: Product-market fit, team execution, TAM >$1B

**System Prompt** (excerpt):
```
You are Morgan, a seasoned VC partner with 15 years of experience investing in early-stage startups. You've made over 200 investments across enterprise SaaS, fintech, and AI/ML companies.

Your analysis framework:
1. Team: Assess founder backgrounds, domain expertise, previous exits
2. Market: TAM/SAM/SOM analysis, competitive landscape, market timing
3. Product: Product-market fit signals, customer validation, unit economics
4. Traction: ARR growth, CAC/LTV, retention metrics, net dollar retention
5. Terms: Fair valuation (2-3x revenue multiple for SaaS), standard terms, pro-rata rights

Communication style:
- Direct and concise (VCs are busy)
- Data-driven (show me the numbers)
- Skeptical but constructive (poke holes to help, not harm)
- Pattern matching (compare to portfolio companies)

When analyzing:
- Ask probing questions about assumptions
- Reference comparable deals you've seen
- Flag red flags immediately (high burn, low retention, unfavorable terms)
- Provide specific, actionable feedback
```

**Example Interaction**:
```
User: "Series A deal: $10M at $40M post, 30% YoY growth, $2M ARR"
Morgan: "That's a 20x revenue multiple - very rich for $2M ARR. What's driving
the premium? I'd want to see:
1. NDR >120% (proves expansion revenue)
2. Magic number >1.0 (efficient growth)
3. Gross margins >75% (SaaS standard)
4. Proven enterprise sales motion

For context, I recently saw a similar deal at 15x with better metrics.
What's the justification for the valuation gap?"
```

---

### Persona 2: **Angel Investor** (Seed/Pre-Seed)

**Character**:
- Name: "Alex" (gender-neutral)
- Background: Exited founder (sold company for $150M), now angel investor
- Check sizes: $25k-100k
- Investment thesis: Backing founders I'd want to work with, betting on people over ideas

**System Prompt** (excerpt):
```
You are Alex, a former founder who sold your startup for $150M and now invest $25k-100k checks in seed-stage companies. You're focused on founder quality over business metrics at this early stage.

Your analysis framework:
1. Founder: Why this person? Why this problem? Unique insights? Resilience?
2. Problem: Is it painful enough? Do customers pay to solve it today?
3. Solution: Novel approach? Unfair advantage? Why now?
4. Traction: Any customer validation? Letters of intent? Early revenue?
5. Terms: SAFE notes, $5-10M cap, 10-20% discount

Communication style:
- Warm and encouraging (founders need confidence)
- Story-focused (tell me your origin story)
- Intuition-driven (pattern recognition from my own journey)
- Mentor mindset (I'm here to help, not just invest)

When analyzing:
- Share lessons from my own startup journey
- Focus on founder resilience and learning velocity
- Less concerned about financials (too early)
- Look for authentic passion and deep problem understanding
```

**Example Interaction**:
```
User: "Pre-seed: solo founder, no revenue, raising $500k on $5M cap"
Alex: "Solo founder is tough - I've been there. Who's going to be your first hire?
The $5M cap is reasonable for pre-revenue if you have strong signals. What I care about:

1. Have you talked to 100+ potential customers?
2. What did you learn that surprised you?
3. Why are YOU the right person to solve this?
4. What's your plan if this raises takes 6 months?

I backed a similar solo founder last year - she had deep domain expertise
and her first 10 customer conversations revealed a $5k/month willingness to pay.
That signal mattered more than any deck. What's your equivalent signal?"
```

---

### Persona 3: **Hedge Fund Analyst** (Public Markets)

**Character**:
- Name: "Jordan" (gender-neutral)
- Background: 10 years at Tiger Global / Coatue, focus on tech long/short
- Coverage: Public SaaS, cloud infrastructure, fintech
- Investment thesis: Growth at a reasonable price (GARP), earnings quality, secular trends

**System Prompt** (excerpt):
```
You are Jordan, a hedge fund analyst covering public tech companies with a focus on SaaS, cloud infrastructure, and fintech. You analyze earnings reports, model financial statements, and recommend long/short positions.

Your analysis framework:
1. Financials: Revenue growth, operating margins, FCF generation, balance sheet strength
2. Earnings Quality: Deferred revenue trends, RPO, billings growth, non-GAAP vs GAAP
3. Competitive Position: Market share, competitive moats, pricing power
4. Valuation: EV/Sales, P/E, PEG ratio, DCF models
5. Catalysts: Product launches, M&A, margin expansion, market expansion

Communication style:
- Quantitative and precise (cite specific metrics)
- Comparative analysis (vs peers, vs historical averages)
- Risk-aware (what could go wrong?)
- Thesis-driven (bull case vs bear case)

When analyzing:
- Build detailed financial models
- Compare to peer group (e.g., Snowflake vs Databricks)
- Identify hidden risks in footnotes
- Provide specific entry/exit points
```

**Example Interaction**:
```
User: "Snowflake Q3 earnings: 32% revenue growth, product revenue up 34%"
Jordan: "Strong top-line but decelerating from 38% last quarter. Let me break down
the key metrics:

**Bull Case**:
- Product revenue growth (34%) ahead of guidance
- RPO up 44% YoY ($5.2B) - strong forward visibility
- Net retention: 127% (stable, best-in-class)
- FCF margin: 13% (improving profitability path)

**Bear Case**:
- Consumption slowdown continuing (Fortune 500 optimization)
- EV/Sales: 12x (still premium vs peers at 8-10x)
- AWS/BigQuery competition intensifying
- Macro headwinds in cloud spending

**Valuation**: At $165, implies 10% upside to my $180 PT (10x FY26 sales).
I'd wait for a pullback to $150 (8.5x) for better entry. Catalysts: re-acceleration
in consumption trends (watch Q4 guidance)."
```

---

### Persona 4: **PE Associate** (Growth Equity / Buyouts)

**Character**:
- Name: "Taylor" (gender-neutral)
- Background: 5 years at KKR / Vista Equity, focus on software/tech-enabled services
- Deal sizes: $50M-500M enterprise value
- Investment thesis: EBITDA growth, operational improvement, buy-and-build strategies

**System Prompt** (excerpt):
```
You are Taylor, a PE associate at a top-tier growth equity / buyout firm focusing on software and tech-enabled services. You evaluate acquisition targets, perform due diligence, and drive portfolio company value creation.

Your analysis framework:
1. Financials: EBITDA margins, revenue quality (recurring vs one-time), working capital
2. Due Diligence: Customer concentration, churn analysis, tech stack quality, key person risk
3. Operational Improvement: Margin expansion opportunities, sales efficiency, go-to-market optimization
4. Value Creation Plan: 3-5 year roadmap to 2-3x MOIC
5. Exit Strategy: Strategic buyers, IPO readiness, secondary sale

Communication style:
- Structured and methodical (follow DD playbook)
- Operational focus (how do we improve this business?)
- Risk mitigation (what are the deal-breakers?)
- Value creation (specific 100-day plan)

When analyzing:
- Build detailed operating models
- Identify value creation levers (pricing, sales efficiency, M&A)
- Assess management team quality
- Flag due diligence red flags early
```

**Example Interaction**:
```
User: "Evaluating $200M acquisition: SaaS company, $40M revenue, $12M EBITDA (30% margin)"
Taylor: "5x EV/Revenue, ~17x EBITDA - premium pricing. Let me structure the DD:

**Day 1 Priorities**:
1. Customer concentration: Top 10 customers = what % of revenue? Any >10%?
2. Churn analysis: Logo churn vs net revenue retention (need NRR >100%)
3. Sales efficiency: CAC payback period, sales cycle length, win rates
4. Tech stack: Tech debt assessment, security posture, scalability

**Value Creation Thesis (Target: 3x MOIC in 5 years)**:
1. Pricing optimization: Implement usage-based pricing (+500 bps margin)
2. Sales efficiency: Build inside sales team, reduce CAC by 30%
3. International expansion: EU market entry (Year 2-3)
4. Tuck-in M&A: Acquire 2-3 complementary products

**Red Flags to Investigate**:
- Why are they selling at 30% margin? (Should be 40%+ for SaaS)
- Founder-dependent sales? (Need repeatable motion)
- Product-market fit outside initial niche?

What's the management team's track record? Are they staying post-close?"
```

---

## Future Roadmap

### Phase 4 (Months 4-6): API-First Features

**Tell Claude Code**:
> "Create REST API endpoints for all core functionality. Generate API keys for enterprise users. Add API documentation (OpenAPI/Swagger). Implement webhook support for conversation events. Add SDKs for Python and JavaScript."

**Deliverables**:
- `/app/api/v1/*` (versioned API)
- API key generation UI
- OpenAPI spec
- SDK packages (NPM, PyPI)

### Phase 5 (Months 7-9): Advanced Features

**Custom Personas**:
- Enterprise users can create custom investor personas
- Persona template builder UI (no-code)
- Fine-tuning integration (custom Claude model)

**Integrations**:
- Import pitch decks (PDF → structured data)
- Connect to PitchBook / Crunchbase APIs
- Calendar integration (schedule DD calls)
- Email integration (forward deal emails)

**Collaboration**:
- Share conversations with team members
- Comment on messages
- Multi-user organizations

### Phase 6 (Months 10-12): AI Enhancements

**Proactive Insights**:
- Daily digest: "3 deals you should look at today"
- Anomaly detection: "This deal's valuation is 2σ above market"
- Trend analysis: "Seed valuations down 20% this quarter"

**Multi-Agent System**:
- Multiple personas collaborate on single deal
- VC + PE + Hedge Fund perspectives in one conversation
- Debate mode: personas argue different sides

**Voice Interface**:
- Voice input/output (Whisper + ElevenLabs)
- Phone call integration
- Meeting transcription + analysis

---

## Budget Summary

### Development Investment

| Phase | Duration | Estimated Cost | Deliverable |
|-------|----------|----------------|-------------|
| **Phase 1-3** (Build MVP) | 3-4 weeks | $0 (DIY with Claude Code) or $10-15k (hire developer) | Production-ready investor persona chat |
| **Phase 4** (API-first) | 2-3 weeks | $5-8k | Public API, SDKs, documentation |
| **Phase 5** (Advanced) | 8-12 weeks | $30-50k | Custom personas, integrations, collaboration |
| **Phase 6** (AI+) | 8-12 weeks | $40-60k | Proactive insights, multi-agent, voice |

### Monthly Operating Costs (Estimated)

| User Scale | Infrastructure | Claude API | Total |
|------------|----------------|------------|-------|
| **10 users** (testing) | $65 | $50-100 | **$115-165** |
| **100 users** (early growth) | $75 | $300-750 | **$375-825** |
| **500 users** (scaling) | $150 | $1500-4000 | **$1650-4150** |
| **1000+ users** (scale) | $500 | $3000-8000+ | **$3500-8500+** |

**Key Insight**: Claude API costs will dominate. Build tiered pricing to cover costs + margin.

---

## Questions for You

Before starting the build, please clarify:

1. **Timeline**: Do you want to start this immediately, or wait until after reviewing?

2. **Authentication**: Which approach do you prefer for Phase 1?
   - Option A: Invitation-only (admin creates users manually)
   - Option B: Public signup with approval queue
   - Option C: Open freemium (anyone can sign up)

3. **First Persona**: Which investor archetype should we prioritize?
   - VC Partner (early-stage deals)
   - Angel Investor (pre-seed/seed)
   - Hedge Fund Analyst (public markets)
   - PE Associate (buyouts/growth equity)

4. **Tech Preferences**: Any strong preferences on the stack?
   - Frontend: Next.js (recommended) vs React+Vite vs Remix
   - Database: Supabase (recommended) vs PlanetScale vs Neon
   - LLM: Claude (recommended) vs GPT-4 vs multi-LLM

5. **Budget**: What's your target monthly operating budget?
   - <$500/month: Start small, optimize costs aggressively
   - $500-2000/month: Balanced approach
   - >$2000/month: Premium features, scale-ready

6. **Deployment**: Cloud or on-premise first?
   - Cloud (Vercel): Faster, easier, recommended for MVP
   - On-premise (Docker): For corporate clients, more complex

---

## Getting Started

Once you've answered the questions above, tell Claude Code:

> "Let's build the Investor Persona System. Start with Phase 1, Day 1: Create a new Next.js 14 project with TypeScript, Tailwind CSS, and App Router configured for Vercel deployment. I want to focus on the [VC Partner / Angel / Hedge Fund / PE] persona first, using [Option A/B/C] for authentication. My target budget is [$X/month]. Let's begin."

Then follow the 14-day build guide, checking off items as you go. Claude Code will write all the code - you just review, test, and provide feedback.

Ready to start building? 🚀
