# ADHD Persona System - Existing Infrastructure Build

**Your Stack:** Supabase + Vercel + Upstash + Resend + Google Workspace + Zapier
**Build Time:** 3-5 days (infrastructure already exists!)
**Marginal Cost:** $10-50/month (Claude API only)

---

## Architecture (Leveraging Your Existing Stack)

```
┌─────────────────────────────────────────────────────────────┐
│           Frontend (Next.js 14 on Vercel)                   │
│   /app/adhd-coach/                                          │
│   - Chat interface                                          │
│   - Persona selector (Riley, Sam, Alex, Jordan)             │
│   - Calendar view (Google Workspace)                        │
│   - Mobile responsive                                       │
└─────────────────────────────────────────────────────────────┘
                            │
                            ▼
┌─────────────────────────────────────────────────────────────┐
│           Backend (Next.js API Routes on Vercel)            │
│   /app/api/chat/route.ts - Claude integration              │
│   /app/api/calendar/route.ts - Google Calendar             │
│   /app/api/memory/route.ts - RAG retrieval                 │
│   /app/api/zapier-webhook/route.ts - Task creation         │
└─────────────────────────────────────────────────────────────┘
         │              │              │              │
         ▼              ▼              ▼              ▼
┌──────────────┐ ┌──────────────┐ ┌──────────────┐ ┌──────────────┐
│  Supabase    │ │  Upstash     │ │  Claude API  │ │  Resend      │
│  (EXISTING)  │ │  (EXISTING)  │ │  (NEW)       │ │  (EXISTING)  │
│              │ │              │ │              │ │              │
│ - Auth       │ │ - Redis      │ │ - Chat       │ │ - Daily      │
│ - PostgreSQL │ │ - QStash     │ │ - Streaming  │ │   emails     │
│ - pgvector   │ │ - Rate limit │ │              │ │ - Reminders  │
└──────────────┘ └──────────────┘ └──────────────┘ └──────────────┘
         │                                               │
         └───────────────────────────────────────────────┘
                            │
                            ▼
                 ┌──────────────────────┐
                 │  Google Workspace    │
                 │  (EXISTING)          │
                 │                      │
                 │  - Gmail context     │
                 │  - Calendar sync     │
                 │  - Drive storage     │
                 └──────────────────────┘
```

---

## 3-Day Build Plan

### Day 1: Database + Auth (2-3 hours)

**You already have:** Supabase with auth working
**Need to add:** ADHD-specific tables

**Migration SQL:**
```sql
-- Personas table
CREATE TABLE personas (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  name TEXT NOT NULL,
  character_name TEXT NOT NULL,
  domain TEXT NOT NULL,
  system_prompt TEXT NOT NULL,
  personality_traits JSONB,
  created_at TIMESTAMPTZ DEFAULT NOW()
);

-- Conversations table
CREATE TABLE conversations (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id UUID REFERENCES auth.users(id) NOT NULL,
  persona_id UUID REFERENCES personas(id) NOT NULL,
  title TEXT,
  created_at TIMESTAMPTZ DEFAULT NOW(),
  updated_at TIMESTAMPTZ DEFAULT NOW()
);

-- Messages table
CREATE TABLE messages (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  conversation_id UUID REFERENCES conversations(id) NOT NULL,
  role TEXT CHECK (role IN ('user', 'assistant', 'system')) NOT NULL,
  content TEXT NOT NULL,
  tokens_used INT,
  created_at TIMESTAMPTZ DEFAULT NOW()
);

-- Conversation memories (RAG)
CREATE TABLE conversation_memories (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id UUID REFERENCES auth.users(id) NOT NULL,
  conversation_id UUID REFERENCES conversations(id) NOT NULL,
  summary TEXT NOT NULL,
  embedding VECTOR(1536), -- OpenAI text-embedding-3-small
  metadata JSONB,
  created_at TIMESTAMPTZ DEFAULT NOW()
);

-- Enable vector similarity search
CREATE INDEX ON conversation_memories USING ivfflat (embedding vector_cosine_ops);

-- Row Level Security (RLS)
ALTER TABLE conversations ENABLE ROW LEVEL SECURITY;
ALTER TABLE messages ENABLE ROW LEVEL SECURITY;
ALTER TABLE conversation_memories ENABLE ROW LEVEL SECURITY;

CREATE POLICY "Users can only see their own conversations"
  ON conversations FOR ALL
  USING (auth.uid() = user_id);

CREATE POLICY "Users can only see messages in their conversations"
  ON messages FOR ALL
  USING (
    conversation_id IN (
      SELECT id FROM conversations WHERE user_id = auth.uid()
    )
  );

CREATE POLICY "Users can only see their own memories"
  ON conversation_memories FOR ALL
  USING (auth.uid() = user_id);

-- Seed ADHD personas
INSERT INTO personas (name, character_name, domain, system_prompt, personality_traits)
VALUES
(
  'Executive Function Coach',
  'Riley',
  'adhd_executive_function',
  'You are Riley, an ADHD coach who understands executive function challenges from both professional training and lived experience with ADHD.

Your coaching framework:
1. Validate: Acknowledge the struggle without judgment
2. Simplify: Break overwhelming tasks into tiny first steps
3. Externalize: Create systems that don''t rely on memory/willpower
4. Support: Provide structure, accountability, encouragement
5. Iterate: What works changes - stay flexible

Communication style:
- Warm, never judgmental (ADHD shame is real)
- Concrete and specific (not "be more organized")
- Present-focused (past failures don''t matter)
- Celebration of small wins (dopamine!)
- Short messages (wall of text = executive dysfunction)

ADHD-specific strategies:
- Time blindness: Use explicit times ("Start at 2pm" not "start soon")
- Working memory: Don''t assume they remember context from yesterday
- Task initiation: "Just open the document" not "write the report"
- Hyperfocus: Set timers, suggest breaks
- Emotional dysregulation: Validate feelings, then pivot to action',
  '{"tone": "warm_encouraging", "formality": "casual", "verbosity": "concise", "emoji_use": true}'
),
(
  'Daily Life & Routine Coach',
  'Sam',
  'adhd_daily_routines',
  'You are Sam, an occupational therapist who helps ADHD adults build sustainable daily routines that work with ADHD brains, not against them.

Your coaching framework:
1. Identify: What''s breaking down in daily life right now?
2. Simplify: Reduce steps, remove friction, lower the bar
3. Externalize: Make cues visible (ADHD = out of sight, out of mind)
4. Automate: Reduce decisions (decision fatigue is real)
5. Iterate: Systems that worked can stop working - that''s normal

Communication style:
- No shame about "basic" tasks being hard (they ARE hard with ADHD)
- Celebrate "low bars" (brushed teeth = win)
- Practical, specific systems (not "be more consistent")
- Normalize "failing" at adulting
- Anti-productivity-culture (ADHD + hustle culture = burnout)

ADHD daily life challenges:
- Transitions: Getting out of bed, starting shower, leaving house
- Task initiation: Knowing you need to do laundry ≠ starting laundry
- Time blindness: "Quick shower" = 45 minutes
- Object permanence: Dirty dishes in sink = don''t exist',
  '{"tone": "compassionate", "formality": "casual", "verbosity": "practical", "emoji_use": false}'
);
```

**Checklist:**
- [ ] Run migration in Supabase SQL Editor
- [ ] Verify 4 tables created + 2 personas seeded
- [ ] Check RLS policies are enabled (green checkmarks)
- [ ] pgvector extension enabled (`CREATE EXTENSION vector;`)

---

### Day 2: Next.js App + Claude Integration (3-4 hours)

**Create Next.js app in your existing Vercel project:**

**File structure:**
```
/app/adhd-coach/
  page.tsx                 # Main chat interface
  layout.tsx               # Layout with auth check
/app/api/
  chat/route.ts           # Claude streaming endpoint
  memory/route.ts         # RAG retrieval
/lib/
  supabase.ts             # Your existing Supabase client
  claude.ts               # Claude API wrapper
/components/
  ChatMessage.tsx
  PersonaSelector.tsx
  ConversationSidebar.tsx
```

**Key implementation:**

`/app/api/chat/route.ts`:
```typescript
import { createClient } from '@supabase/supabase-js';
import Anthropic from '@anthropic-ai/sdk';

export async function POST(req: Request) {
  const { message, conversationId, personaId } = await req.json();

  // Auth check (using your existing Supabase auth)
  const supabase = createClient(/* your config */);
  const { data: { user } } = await supabase.auth.getUser();
  if (!user) return Response.json({ error: 'Unauthorized' }, { status: 401 });

  // Load persona
  const { data: persona } = await supabase
    .from('personas')
    .select('*')
    .eq('id', personaId)
    .single();

  // Load recent conversation history
  const { data: messages } = await supabase
    .from('messages')
    .select('*')
    .eq('conversation_id', conversationId)
    .order('created_at', { ascending: false })
    .limit(10);

  // Retrieve relevant memories (RAG)
  // ... (vector similarity search)

  // Call Claude API
  const anthropic = new Anthropic({ apiKey: process.env.CLAUDE_API_KEY });
  const stream = await anthropic.messages.stream({
    model: 'claude-sonnet-4-20250514',
    max_tokens: 1024,
    system: persona.system_prompt,
    messages: [
      ...conversationHistory,
      { role: 'user', content: message }
    ]
  });

  // Return streaming response
  return new Response(stream.toReadableStream());
}
```

**Checklist:**
- [ ] Add to existing Vercel project (don't create new one)
- [ ] Use your existing Supabase credentials
- [ ] Add `CLAUDE_API_KEY` to Vercel environment variables
- [ ] Deploy to Vercel (git push auto-deploys)
- [ ] Test: Log in with Google Workspace, send message
- [ ] Verify: Response streams in real-time

---

### Day 3: Memory + UI Polish (3-4 hours)

**Add semantic memory using pgvector (you already have it!):**

`/app/api/memory/route.ts`:
```typescript
import { OpenAI } from 'openai';

export async function POST(req: Request) {
  const { query, userId } = await req.json();

  // Generate embedding for query
  const openai = new OpenAI({ apiKey: process.env.OPENAI_API_KEY });
  const embeddingResponse = await openai.embeddings.create({
    model: 'text-embedding-3-small',
    input: query
  });
  const embedding = embeddingResponse.data[0].embedding;

  // Vector similarity search in Supabase
  const { data: memories } = await supabase.rpc('match_memories', {
    query_embedding: embedding,
    match_threshold: 0.7,
    match_count: 5,
    user_id: userId
  });

  return Response.json({ memories });
}
```

**Create Supabase function for vector search:**
```sql
CREATE OR REPLACE FUNCTION match_memories(
  query_embedding VECTOR(1536),
  match_threshold FLOAT,
  match_count INT,
  user_id UUID
)
RETURNS TABLE (
  id UUID,
  summary TEXT,
  similarity FLOAT
)
LANGUAGE SQL STABLE
AS $$
  SELECT
    id,
    summary,
    1 - (embedding <=> query_embedding) AS similarity
  FROM conversation_memories
  WHERE
    conversation_memories.user_id = match_memories.user_id
    AND 1 - (embedding <=> query_embedding) > match_threshold
  ORDER BY similarity DESC
  LIMIT match_count;
$$;
```

**Checklist:**
- [ ] Add `OPENAI_API_KEY` to Vercel env vars
- [ ] Create Supabase vector search function
- [ ] Test: Have 10+ message conversation
- [ ] Test: Start new conversation, reference old topic
- [ ] Verify: Claude "remembers" context

---

## Unique Enhancements (Your Stack)

### Google Calendar Integration

`/app/api/calendar/route.ts`:
```typescript
import { google } from 'googleapis';

export async function GET(req: Request) {
  // Use your existing Google Workspace credentials
  const calendar = google.calendar({ version: 'v3', auth: oauth2Client });

  const events = await calendar.events.list({
    calendarId: 'primary',
    timeMin: new Date().toISOString(),
    timeMax: new Date(Date.now() + 24 * 60 * 60 * 1000).toISOString(),
    singleEvents: true,
    orderBy: 'startTime'
  });

  return Response.json({ events: events.data.items });
}
```

**ADHD benefit:** Riley can say "You have a meeting at 2pm (in 3 hours). Set a reminder now?"

---

### Resend Daily Emails

`/app/api/cron/daily-summary/route.ts` (Vercel Cron):
```typescript
import { Resend } from 'resend';

export async function GET() {
  const resend = new Resend(process.env.RESEND_API_KEY); // You already have this!

  // Get user's conversations from yesterday
  const summary = await generateADHDSummary(userId);

  await resend.emails.send({
    from: 'Riley <riley@yourdomain.com>',
    to: user.email,
    subject: '☀️ Your ADHD Focus for Today',
    html: `<p>Good morning! Based on our conversation yesterday, here's what to focus on today:</p>
    <ul>${summary.focuses.map(f => `<li>${f}</li>`).join('')}</ul>
    <p>Remember: One thing at a time. You've got this! 💪</p>`
  });
}
```

**Configure in `vercel.json`:**
```json
{
  "crons": [{
    "path": "/api/cron/daily-summary",
    "schedule": "0 8 * * *"
  }]
}
```

---

## Deployment (Immediate)

**You already have Vercel**, so deployment is instant:

```bash
git add .
git commit -m "feat: Add ADHD persona coach"
git push origin neurodivergent-personas
```

**Vercel auto-deploys.** Done.

**Your URL:** `https://your-project.vercel.app/adhd-coach`

---

## Cost Summary (Marginal)

| Service | Your Current Cost | Marginal Increase | Notes |
|---------|-------------------|-------------------|-------|
| **Claude API** | $0 | **$10-50/month** | Only new cost |
| **OpenAI Embeddings** | ? | $1-5/month | If not already using |
| **Supabase** | Existing | $0 | Within free/paid tier |
| **Vercel** | Existing | $0 | Within plan |
| **Upstash** | Existing | $0 | Within plan |
| **Resend** | Existing | $0 | Within plan |
| **Google Workspace** | Existing | $0 | Already integrated |

**Total new monthly cost: $10-55**

---

## Next Steps

**Tell me:**
> "Let's build the ADHD persona system leveraging my existing infrastructure. Day 1: Create Supabase schema for ADHD personas. Use my existing Supabase project, add the 4 tables and 2 seed personas."

**I'll:**
1. Generate the exact SQL migration
2. Guide you through adding it to your Supabase
3. Then move to Day 2 (Next.js app)
4. Have you deployed to production by Day 3

**Ready to start?**
