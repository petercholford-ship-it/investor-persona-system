# Build Instructions for VS Code Claude CLI

**Target:** VS Code Claude CLI with MCP tools (Vercel, Supabase, Playwright, Render)
**Project:** ADHD Research Persona System
**Branch:** neurodivergent-personas
**Timeline:** 3-4 days (12-16 hours total)

---

## Prerequisites Checklist

Before starting, verify you have:

- [ ] Access to existing Supabase project (from Aida)
- [ ] Access to existing Vercel project (from Aida)
- [ ] Anthropic API key (`CLAUDE_API_KEY`)
- [ ] OpenAI API key (`OPENAI_API_KEY`) - for embeddings (optional for MVP)
- [ ] GitHub access (already configured)
- [ ] VS Code with Claude CLI and MCP tools installed

---

## Day 1: Database Setup (2-3 hours)

### Step 1.1: Run Supabase Migration

**File:** `supabase/migrations/001_research_personas.sql`

**Instructions:**
1. Open your existing Supabase project dashboard
2. Navigate to SQL Editor
3. Copy entire contents of `001_research_personas.sql`
4. Paste into SQL Editor
5. Click "Run"

**What this creates:**
- 5 tables: `research_personas`, `research_sessions`, `session_participants`, `research_messages`, `research_insights`
- 5 ADHD behavioral archetypes (Jamie, Taylor, Sam, Alex, Riley)
- RLS policies (only authenticated users can access)
- Helper functions

**Verification:**
```sql
-- Run this to verify personas were created:
SELECT name, archetype FROM research_personas;

-- Expected output:
-- Jamie - The Overthinker | overthinker
-- Taylor - The Hyperfocuser | hyperfocuser
-- Sam - The Time-Blind Optimist | time_blind
-- Alex - The Rejection-Sensitive | rejection_sensitive
-- Riley - The Task-Switching Chaos | task_switcher
```

**Checklist:**
- [ ] 5 tables created in Supabase
- [ ] 5 personas seeded
- [ ] RLS policies enabled (green checkmarks in Supabase table view)
- [ ] pgvector extension enabled

---

### Step 1.2: Update TypeScript Types

**Using Supabase CLI MCP tool:**

```bash
# Generate TypeScript types from Supabase schema
supabase gen types typescript --project-id <your-project-id> > types/database.ts
```

**Verification:**
- [ ] `types/database.ts` file created
- [ ] Contains `ResearchPersonas`, `ResearchSessions`, etc. types

---

## Day 2: Next.js App Structure (3-4 hours)

### Step 2.1: Create Directory Structure

Create the following directories and files:

```
/app/research/                          # Research interface root
  page.tsx                              # Dashboard: list sessions, create new
  layout.tsx                            # Auth check, research-specific layout

/app/research/individual/[id]/          # Individual interview mode
  page.tsx

/app/research/focus-group/[id]/         # Focus group mode
  page.tsx

/app/research/user-interview/[id]/      # Persona interviews user
  page.tsx

/app/api/research/
  personas/route.ts                     # GET all personas
  sessions/route.ts                     # POST create session, GET list sessions
  sessions/[id]/route.ts                # GET session detail
  chat/route.ts                         # POST send message (handles all modes)
  insights/route.ts                     # POST tag insight

/components/research/
  PersonaCard.tsx                       # Display persona info
  SessionCard.tsx                       # Display session in list
  ChatInterface.tsx                     # Shared chat UI
  InsightTagger.tsx                     # Tag insights during conversation
  FocusGroupDisplay.tsx                 # Special display for multiple persona responses

/lib/research/
  supabase.ts                           # Supabase client (reuse from Aida)
  claude.ts                             # Claude API wrapper
  types.ts                              # Shared TypeScript types
```

**Using VS Code:**
- Use file creation commands to create this structure
- Use your existing Aida codebase as reference for auth patterns

---

### Step 2.2: Create Research Dashboard

**File:** `/app/research/page.tsx`

**Implementation:**
```typescript
// This should:
// 1. Check authentication (reuse Aida's OAuth)
// 2. List user's research sessions
// 3. Show "Create New Session" with mode selection
// 4. Display session cards with preview

// Pseudocode:
export default async function ResearchDashboard() {
  // Get authenticated user
  const { data: { user } } = await supabase.auth.getUser();

  // Fetch user's sessions
  const { data: sessions } = await supabase
    .from('research_sessions')
    .select('*')
    .order('created_at', { ascending: false });

  return (
    <div>
      <h1>ADHD Research Sessions</h1>
      <CreateSessionButton />
      <SessionList sessions={sessions} />
    </div>
  );
}
```

**Checklist:**
- [ ] Dashboard displays for authenticated users
- [ ] Shows list of sessions
- [ ] Has "Create New Session" button
- [ ] Clicking session navigates to appropriate mode

---

### Step 2.3: Create Session Creation Modal

**Component:** Modal to select session type and personas

**Options:**
1. **Individual Interview** → Select 1 persona → `/research/individual/[id]`
2. **Focus Group** → Select 2-5 personas → `/research/focus-group/[id]`
3. **User Interview** → Select 1 persona to act as interviewer → `/research/user-interview/[id]`

**API call:**
```typescript
const response = await fetch('/api/research/sessions', {
  method: 'POST',
  body: JSON.stringify({
    session_type: 'focus_group',
    title: 'Test Aida onboarding mockup',
    research_goal: 'Get reactions to onboarding flow',
    persona_ids: ['uuid1', 'uuid2', 'uuid3'] // Jamie, Taylor, Sam
  })
});
```

---

## Day 3: Chat Implementation (4-5 hours)

### Step 3.1: Individual Interview Mode

**File:** `/app/research/individual/[id]/page.tsx`

**Features:**
- Load session and persona
- Display persona card (name, archetype, description)
- Chat interface (you ask, persona responds)
- Tag insights as you go
- Export transcript

**Implementation:**
```typescript
export default async function IndividualInterview({ params }) {
  const session = await getSession(params.id);
  const persona = await getSessionPersona(params.id);

  return (
    <div className="grid grid-cols-3">
      <aside>
        <PersonaCard persona={persona} />
        <InsightTagger sessionId={session.id} />
      </aside>
      <main className="col-span-2">
        <ChatInterface
          sessionId={session.id}
          personaId={persona.id}
          mode="individual"
        />
      </main>
    </div>
  );
}
```

**Checklist:**
- [ ] Can select persona
- [ ] Can type question
- [ ] Persona responds in character
- [ ] Responses feel authentic to archetype
- [ ] Can tag insights

---

### Step 3.2: Focus Group Mode

**File:** `/app/research/focus-group/[id]/page.tsx`

**Unique Features:**
- Multiple personas visible
- Your question goes to ALL personas
- Each responds in parallel
- Personas can respond to each other
- Follow-up questions can target specific persona or all

**Implementation Flow:**
1. Moderator asks question
2. System sends question to Claude API once per persona (parallel)
3. Collect all responses
4. Display all responses at once
5. Allow targeted follow-ups

**API Endpoint:** `/api/research/chat`

```typescript
// When mode = 'focus_group'
// Send to all personas in parallel

const responses = await Promise.all(
  personas.map(persona =>
    callClaudeAPI({
      system: persona.system_prompt + focusGroupContext,
      messages: conversationHistory,
      userQuestion: message
    })
  )
);

// Save all responses to research_messages
// Return all responses to frontend
```

**Display:**
```
You: "What do you think about this app mockup?"

┌─────────────────────────────────────┐
│ Jamie (Overthinker)                 │
│ "Too many buttons. I'd be paralyzed │
│  trying to decide which to click..."│
└─────────────────────────────────────┘

┌─────────────────────────────────────┐
│ Taylor (Hyperfocuser)               │
│ "I actually like the complexity -   │
│  means I can customize it..."       │
└─────────────────────────────────────┘

┌─────────────────────────────────────┐
│ Sam (Time-Blind)                    │
│ "How long does setup take? If it's  │
│  more than 5 min I'll abandon it..."│
└─────────────────────────────────────┘
```

**Checklist:**
- [ ] Multiple personas display simultaneously
- [ ] All respond to moderator questions
- [ ] Can target specific persona for follow-up
- [ ] Personas have distinct voices
- [ ] Feels like a real focus group

---

### Step 3.3: User Interview Mode

**File:** `/app/research/user-interview/[id]/page.tsx`

**Unique Feature:** Persona asks questions, real user answers

**Flow:**
1. Persona introduces self: "Hi! I'm Jamie, researching ADHD experiences..."
2. Persona asks first question: "What brings you here today?"
3. User types response
4. Persona asks follow-up based on response
5. Continue for 5-10 exchanges
6. Thank user, save transcript

**Implementation:**
```typescript
// Persona's system prompt includes:
// "You are conducting a user research interview.
//  Ask open-ended questions.
//  Follow up on interesting points.
//  Be empathetic and curious.
//  Wrap up after 10 questions."

// On each user response:
const personaQuestion = await callClaudeAPI({
  system: persona.system_prompt + interviewerInstructions,
  messages: conversationHistory,
  userResponse: userMessage
});
```

**Checklist:**
- [ ] Persona acts as interviewer
- [ ] Asks relevant follow-up questions
- [ ] Feels conversational, not robotic
- [ ] Saves complete transcript
- [ ] Easy for real user to navigate

---

## Day 4: Integration & Polish (3-4 hours)

### Step 4.1: Aida Onboarding Integration (Optional)

**Goal:** After user signs up for Aida, optionally interview them

**File:** `/app/onboarding/research-opt-in.tsx` (in Aida codebase)

```typescript
// After user creates account:
export default function ResearchOptIn() {
  const [wantsInterview, setWantsInterview] = useState(false);

  if (wantsInterview) {
    // Create user interview session
    const session = await createUserInterviewSession({
      persona_archetype: 'task_switcher', // Riley
      research_goal: 'Understand why users sign up for Aida',
      real_user_id: user.id
    });

    // Redirect to interview
    router.push(`/research/user-interview/${session.id}`);
  }

  return (
    <div>
      <h2>Help Improve Aida</h2>
      <p>Chat with Riley for 5 minutes about your ADHD experiences?</p>
      <button onClick={() => setWantsInterview(true)}>Sure!</button>
      <button onClick={skipInterview}>Skip</button>
    </div>
  );
}
```

**Checklist:**
- [ ] Opt-in screen after signup
- [ ] Creates user interview session
- [ ] Riley interviews new user
- [ ] Transcript saved to research database
- [ ] User can skip if they want

---

### Step 4.2: Insight Analysis Dashboard

**File:** `/app/research/insights/page.tsx`

**Features:**
- View all tagged insights across sessions
- Filter by tags (pain_point, feature_request, emotional_response)
- Search insights
- Export insights to CSV
- Pattern analysis (AI summary of common themes)

**Implementation:**
```typescript
// Fetch all insights for user's sessions
const insights = await supabase
  .from('research_insights')
  .select('*, research_sessions(*), research_messages(*)')
  .order('created_at', { ascending: false });

// Group by tags
const painPoints = insights.filter(i => i.tags.includes('pain_point'));
const featureRequests = insights.filter(i => i.tags.includes('feature_request'));
```

**Checklist:**
- [ ] Shows all insights
- [ ] Can filter by tag
- [ ] Can search text
- [ ] Export to CSV works
- [ ] Useful for analysis

---

### Step 4.3: Environment Variables

**Add to Vercel:**

Using Vercel MCP tool:
```bash
vercel env add CLAUDE_API_KEY
# Paste your Anthropic API key

vercel env add OPENAI_API_KEY  # Optional: for embeddings
# Paste your OpenAI API key
```

**Verify in Vercel dashboard:**
- [ ] `CLAUDE_API_KEY` set
- [ ] `OPENAI_API_KEY` set (if using embeddings)
- [ ] Existing Supabase env vars still present
- [ ] All environments (dev, preview, production) configured

---

### Step 4.4: Deploy to Vercel

**Using Vercel MCP tool:**

```bash
# Deploy current branch
vercel --prod

# Or link to GitHub and auto-deploy
vercel link
# Then git push triggers deploy
```

**Verification:**
- [ ] Deployment successful
- [ ] Can access `/research` route
- [ ] Authentication works
- [ ] Can create session
- [ ] Chat works
- [ ] No errors in Vercel logs

---

### Step 4.5: Smoke Test with Playwright

**Create test file:** `/tests/research-personas.spec.ts`

```typescript
import { test, expect } from '@playwright/test';

test('can create research session', async ({ page }) => {
  // Login (use existing Aida auth)
  await page.goto('/research');

  // Create new session
  await page.click('text=Create New Session');
  await page.click('text=Individual Interview');
  await page.click('text=Jamie - The Overthinker');
  await page.click('text=Start Interview');

  // Verify session created
  await expect(page.locator('text=Jamie')).toBeVisible();
});

test('focus group shows multiple personas', async ({ page }) => {
  await page.goto('/research');
  await page.click('text=Create New Session');
  await page.click('text=Focus Group');

  // Select 3 personas
  await page.click('text=Jamie');
  await page.click('text=Taylor');
  await page.click('text=Sam');
  await page.click('text=Start Focus Group');

  // Ask question
  await page.fill('textarea', 'What do you think about to-do apps?');
  await page.click('text=Send');

  // Verify all 3 respond
  await expect(page.locator('text=Jamie')).toBeVisible();
  await expect(page.locator('text=Taylor')).toBeVisible();
  await expect(page.locator('text=Sam')).toBeVisible();
});
```

**Run with MCP:**
```bash
playwright test tests/research-personas.spec.ts
```

**Checklist:**
- [ ] Create session test passes
- [ ] Focus group test passes
- [ ] Individual interview test passes
- [ ] No console errors

---

## Completion Checklist

### Database
- [ ] Migration run successfully in Supabase
- [ ] 5 personas seeded
- [ ] RLS policies working
- [ ] TypeScript types generated

### Frontend
- [ ] Research dashboard accessible at `/research`
- [ ] Can create sessions (all 3 modes)
- [ ] Individual interview works
- [ ] Focus group works
- [ ] User interview works
- [ ] Insight tagging works
- [ ] Mobile responsive
- [ ] Authentication works (reusing Aida's OAuth)

### Backend
- [ ] API routes working
- [ ] Claude API integration working
- [ ] Persona responses feel authentic
- [ ] Messages saving to database
- [ ] Insights saving to database

### Deployment
- [ ] Deployed to Vercel
- [ ] Environment variables set
- [ ] No errors in production
- [ ] Smoke tests passing

### Documentation
- [ ] Code commented
- [ ] README updated
- [ ] Example research sessions created
- [ ] Screenshots/demo ready

---

## Usage Examples (After Build)

### Example 1: Individual Interview
```
You → /research → Create New Session → Individual Interview → Select Jamie

You: "Walk me through how you decide which to-do app to use."

Jamie: "Oh god, so I currently have Todoist, Notion, Things, AND TickTick
installed. I've spent literally 40 hours over the last month reading blog
posts comparing them. I have a spreadsheet with pros and cons. But I can't
DECIDE. What if I pick the wrong one and miss something? So I'm paralyzed
and using... none of them. Just a paper notepad. Which I also overthought
buying (grid vs lined vs dotted)."
```

### Example 2: Focus Group
```
You → /research → Create New Session → Focus Group → Select Jamie, Taylor, Alex

You: "React to this app mockup [show image]"

Jamie: "There are 6 buttons on the homepage. Which one do I click first?
I'd spend 10 minutes deciding and then close the app."

Taylor: "I actually like having options! But I'd hyperfocus on customizing
it for 3 hours instead of using it."

Alex: "The error message says 'Invalid input' - that feels harsh. Can you
say 'Oops, try again' instead? Otherwise I'd feel stupid and uninstall."
```

### Example 3: User Interview (Aida Onboarding)
```
[New user signs up for Aida, opts into research interview]

Riley: "Hi! I'm Riley. I'm researching why people try ADHD productivity tools.
Mind chatting for 5 minutes?"

User: "Sure!"

Riley: "Cool! So what made you sign up for Aida today?"

User: "My email is out of control. 3,000 unread."

Riley: "Oof, that's overwhelming. What happens when you open your inbox?"

User: "I just... freeze. It's too much. So I close it."

Riley: "Do you remember the last time you felt in control of your email?"

[Interview continues, transcript saved]
```

---

## Cost Estimates

**Free Tier Usage:**
- Supabase: Within 500MB limit
- Vercel: Within 100GB bandwidth
- Upstash: Within 10k commands/day

**Paid Usage:**
- Claude API: $0.01-0.05 per message
  - Individual interview (10 messages): ~$0.50
  - Focus group (4 personas × 5 exchanges): ~$1.00
  - User interview (10 exchanges): ~$0.50
  - **Monthly (moderate use): $10-30**

**Total marginal cost: $10-30/month**

---

## Support & Troubleshooting

### Common Issues

**Issue:** Personas respond out of character
**Fix:** Review system prompts, add more behavioral examples

**Issue:** Focus group personas all sound the same
**Fix:** Increase temperature in Claude API call (0.7-0.9)

**Issue:** User interview feels robotic
**Fix:** Add conversational examples to persona prompts

**Issue:** Authentication fails
**Fix:** Verify RLS policies, check Supabase auth config

### Need Help?

Refer back to original research persona specifications in:
- `personas/adhd-personas-starter.md`
- `docs/BOOTSTRAP_GUIDE.md`
- `docs/EXISTING_INFRASTRUCTURE_BUILD.md`

---

## Next Steps After Build

1. **Run Research Sessions**
   - Interview personas about Aida features
   - Test messaging and positioning
   - Validate product assumptions

2. **Collect Real User Feedback**
   - Add opt-in to Aida onboarding
   - Interview real users with persona facilitator
   - Compare synthetic vs real responses

3. **Expand Personas**
   - Add autism archetypes
   - Add comorbidity combinations (ADHD + anxiety)
   - Add neurotypical comparisons

4. **Analysis Tools**
   - AI summary of insights
   - Pattern detection across sessions
   - Export for qualitative analysis tools

---

**Good luck! You're building something genuinely useful for ADHD research.**
