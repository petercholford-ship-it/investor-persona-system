# Bootstrap Guide: Build for Next to Nothing (Personal Use)

**Goal:** Get an ADHD persona system running for personal use with minimal cost (< $10-20/month)

---

## Cost Optimization Strategy

### What We're Skipping (For Now)
- ❌ User authentication (it's just you)
- ❌ Multi-user support
- ❌ Admin panel
- ❌ Rate limiting
- ❌ Usage tiers and quotas
- ❌ Payment integration
- ❌ Production deployment (use local first)

### What We're Keeping
- ✅ Chat interface
- ✅ Claude API integration (the core)
- ✅ Conversation history
- ✅ Memory (simplified)
- ✅ Multiple personas

---

## Free Tier Infrastructure

| Service | Free Tier | What We Use It For | Cost if Exceeded |
|---------|-----------|-------------------|------------------|
| **Supabase** | 500 MB database, 1 GB file storage, 2 GB bandwidth | Conversation storage | $25/month (unlikely to hit) |
| **Vercel** | 100 GB bandwidth, hobby project | Local dev only initially | $0 (use local) |
| **Upstash Redis** | 10,000 commands/day | Session cache (optional) | Skip it for personal use |
| **Claude API** | Pay-per-use, no free tier | The AI brain | **$10-50/month** (only real cost) |
| **OpenAI Embeddings** | Pay-per-use | Memory (RAG) - optional for MVP | $0.50-2/month if used |

### Total Monthly Cost: **$10-50** (just Claude API usage)

---

## Simplified Architecture (Bootstrap Version)

```
┌─────────────────────────────────────────────────┐
│   Frontend (Simple HTML + Vanilla JS)          │
│   - Chat interface                              │
│   - Persona selector                            │
│   - Local only (http://localhost:3000)          │
└─────────────────────────────────────────────────┘
                    │
                    ▼
┌─────────────────────────────────────────────────┐
│   Backend (Minimal Express.js Server)           │
│   - /api/chat endpoint                          │
│   - Claude API integration                      │
│   - SQLite database (local file)                │
└─────────────────────────────────────────────────┘
                    │
                    ▼
┌─────────────────────────────────────────────────┐
│   Storage (Free)                                │
│   - SQLite file (conversations.db)              │
│   - JSON files (persona configs)                │
│   - No cloud, all local                         │
└─────────────────────────────────────────────────┘
```

**Why This Is Cheaper:**
- No Supabase costs (SQLite is free, local)
- No Redis costs (skip caching for personal use)
- No Vercel costs (run locally)
- Only pay for Claude API

---

## Minimal Build Plan (3-5 Days)

### Day 1: Bare Minimum Setup (2-3 hours)

**Goal:** Get a basic chat working with Claude

**Tell Claude Code:**
> "Let's build a minimal ADHD persona chat for personal use. Use Express.js backend with SQLite, simple HTML/JS frontend, no auth, no deployment. Just get chat working with Claude API locally."

**What gets created:**
```
adhd-coach/
├── server.js               (Express server)
├── database.js             (SQLite setup)
├── public/
│   ├── index.html          (Chat UI)
│   ├── style.css           (Basic styling)
│   └── app.js              (Frontend JS)
├── personas/
│   └── riley-adhd-coach.json  (Persona config)
├── .env                    (API keys)
└── package.json
```

**Your tasks:**
- [ ] Install Node.js
- [ ] Run `npm install`
- [ ] Add `CLAUDE_API_KEY` to `.env`
- [ ] Run `node server.js`
- [ ] Open `http://localhost:3000`
- [ ] Test sending a message

**Verification:**
- [ ] Can type message and get Claude response
- [ ] Response feels like ADHD coach (uses persona prompt)
- [ ] Conversation saves to SQLite

---

### Day 2: Add Personas (1-2 hours)

**Goal:** Switch between multiple ADHD personas

**Tell Claude Code:**
> "Add persona switching. Create 2-3 ADHD persona configs (Executive Function Coach, Daily Life Coach). Add dropdown to select persona. Load persona system prompt based on selection."

**What gets created:**
- [ ] `personas/riley-executive-function.json`
- [ ] `personas/sam-daily-life.json`
- [ ] Updated `index.html` with persona selector dropdown
- [ ] Backend loads persona config and injects into Claude prompt

**Your tasks:**
- [ ] Customize persona prompts (use adhd-personas-starter.md)
- [ ] Test switching between personas
- [ ] Verify each persona has distinct personality

**Verification:**
- [ ] Dropdown shows 2-3 personas
- [ ] Selecting persona changes tone/approach
- [ ] New conversations use selected persona

---

### Day 3: Conversation History (1-2 hours)

**Goal:** See past conversations and continue them

**Tell Claude Code:**
> "Add conversation management. Show list of past conversations in sidebar. Can start new conversation or continue existing one. Store in SQLite."

**What gets created:**
- [ ] Updated UI with sidebar showing conversation list
- [ ] "New Conversation" button
- [ ] Click conversation to load history
- [ ] Database schema for conversations and messages

**Your tasks:**
- [ ] Test creating multiple conversations
- [ ] Test switching between conversations
- [ ] Verify messages persist after server restart

**Verification:**
- [ ] Can see list of past conversations
- [ ] Can switch between conversations
- [ ] History loads correctly
- [ ] New conversation starts fresh

---

### Day 4: Memory (Optional - Simplified) (2-3 hours)

**Goal:** Basic memory across conversations

**Simplified approach (no RAG, no embeddings, no vectors):**

Instead of complex semantic search, use simple keyword matching:

**Tell Claude Code:**
> "Add simple conversation memory. Before sending to Claude, search past conversations for keywords from current message. Include relevant past messages in context. Use SQLite LIKE queries, no embeddings."

**What gets created:**
- [ ] Keyword extraction from user message
- [ ] SQLite query to find similar past messages
- [ ] Inject top 3-5 relevant past messages into context
- [ ] Display "Remembering..." indicator in UI

**Cost:**
- $0 (no OpenAI embeddings)
- Uses SQLite full-text search (free)

**Your tasks:**
- [ ] Test: Reference something from old conversation
- [ ] Verify Claude "remembers" context

**Verification:**
- [ ] Claude references past conversations when relevant
- [ ] Not perfect (no embeddings) but works well enough
- [ ] No additional API costs

---

### Day 5: Polish (Optional) (1-2 hours)

**Nice-to-haves if you have time:**

- [ ] Better UI styling (make it pleasant to use)
- [ ] Export conversation to text file
- [ ] Dark mode (ADHD-friendly for evening use)
- [ ] Keyboard shortcuts (Enter to send, Cmd+N for new)
- [ ] Timestamps on messages

---

## Super Minimal Version (1 Day)

If you want to test even faster, skip Days 2-5:

**Tell Claude Code:**
> "Build the absolute minimum: Express server, Claude API, single ADHD persona (Executive Function Coach), basic chat interface, no database (messages in memory, lost on restart). Just prove the concept works."

**Result in 2-3 hours:**
- Basic chat works
- Uses ADHD persona
- No persistence (refresh = lose history)
- But you can test if the persona is helpful

**Then decide:** Is this useful? If yes, add features incrementally.

---

## Cost Breakdown (Personal Use)

### One-Time Costs
- $0 (all free/open-source tools)

### Monthly Costs

**Claude API** (only real cost):
```
Personal usage estimate: 10 conversations/week, 10 messages each
= 40 conversations/month
= ~400 messages/month

Cost per message (typical ADHD conversation):
- Input: User message (50 tokens) + persona prompt (500 tokens) + history (500 tokens) = 1,050 tokens
- Output: Claude response (300 tokens average)

Monthly cost:
Input: 1,050 tokens × 400 messages = 420,000 tokens = $1.26 (at $0.003/1k tokens)
Output: 300 tokens × 400 messages = 120,000 tokens = $1.80 (at $0.015/1k tokens)
Total: ~$3-5/month
```

**Realistic personal use:** $5-15/month

**Heavy use (daily, long conversations):** $15-30/month

**Worst case (using it constantly):** $30-50/month

### Free Options
- **SQLite**: Free, local database
- **Express.js**: Free, open-source
- **No hosting**: Run locally, $0

---

## Optimization Strategies

### Reduce Claude API Costs

**1. Shorter Persona Prompts (500 tokens → 300 tokens)**
- Keep core personality and framework
- Remove examples and extended explanations
- Still effective but cheaper

**2. Limit Conversation History (20 messages → 10 messages)**
- Send less context to Claude
- Still maintains recent continuity
- Cuts input tokens in half

**3. Use Shorter Responses**
- Set max_tokens to 500 instead of 2000
- ADHD-friendly (shorter is better anyway)
- Cuts output costs by 75%

**Result:** $5/month → $2-3/month

### Skip Expensive Features (For Now)

**Don't need for personal use:**
- ❌ RAG/Embeddings (OpenAI costs)
  - Use simple keyword search instead (free)
- ❌ Redis caching (Upstash costs)
  - Personal use doesn't need caching
- ❌ Hosted database (Supabase costs after free tier)
  - SQLite file is fine for personal use

---

## When to Upgrade

**Stick with bootstrap version if:**
- ✅ It's just you using it
- ✅ Running locally is fine
- ✅ Simple keyword memory is good enough
- ✅ Monthly cost is < $20

**Consider full build when:**
- ❌ Want to share with others (need auth, user management)
- ❌ Need mobile access (requires deployment)
- ❌ Want better memory (semantic search)
- ❌ Using multiple devices (need cloud sync)

---

## Tech Stack (Bootstrap)

### Super Simple Stack
```
Backend:  Express.js (Node.js)
Frontend: HTML + Vanilla JavaScript (no framework)
Database: SQLite (local file)
AI:       Claude 3.5 Sonnet (Anthropic)
Memory:   SQLite full-text search (no embeddings)
Hosting:  localhost (no deployment)
```

**Why these choices:**
- **Express**: Minimal, easy to understand
- **Vanilla JS**: No build step, no complexity
- **SQLite**: Zero config, works out of the box
- **Claude**: Best reasoning for ADHD coaching
- **Local hosting**: Free, fast, private

### Alternative: Even Simpler (No Backend)

If you want ZERO code:

**Option 1: Use Claude.ai Directly**
- Go to claude.ai
- Create "Projects" for each persona
- Add persona prompt as Project instructions
- Free tier: Limited messages, but works

**Cost:** $0 (free tier) or $20/month (Pro)

**Downside:** No conversation history, no memory across sessions, can't switch personas mid-conversation

**Option 2: Use ChatGPT Custom GPTs**
- Create Custom GPT for each ADHD persona
- Add persona prompt as instructions
- ChatGPT Plus required: $20/month

**Cost:** $20/month

---

## Bootstrap Build Checklist

### Prerequisites
- [ ] Node.js installed
- [ ] Anthropic API key (get from console.anthropic.com)
- [ ] Code editor (VS Code)
- [ ] Terminal/command line access

### Phase 1: Minimal Viable Persona (Day 1)
- [ ] Create project folder
- [ ] Initialize Node.js project
- [ ] Install dependencies (express, @anthropic-ai/sdk, sqlite3)
- [ ] Create basic Express server
- [ ] Add Claude API integration
- [ ] Create simple HTML chat interface
- [ ] Test: Can send message and get response

### Phase 2: Multi-Persona (Day 2)
- [ ] Create persona config files (JSON)
- [ ] Add persona selector dropdown
- [ ] Load persona prompt based on selection
- [ ] Test: Each persona feels different

### Phase 3: Persistence (Day 3)
- [ ] Set up SQLite database
- [ ] Save conversations and messages
- [ ] Show conversation list in sidebar
- [ ] Load conversation history
- [ ] Test: Messages persist after restart

### Phase 4: Memory (Day 4 - Optional)
- [ ] Add simple keyword search
- [ ] Retrieve relevant past messages
- [ ] Include in Claude context
- [ ] Test: Claude references past conversations

### Phase 5: Polish (Day 5 - Optional)
- [ ] Improve UI/UX
- [ ] Add keyboard shortcuts
- [ ] Add export functionality
- [ ] Dark mode

---

## Sample File Structure

```
adhd-coach/
├── server.js                 # Express server
├── database.js               # SQLite setup and queries
├── package.json              # Dependencies
├── .env                      # API keys (not in git)
├── .gitignore
├── README.md
├── conversations.db          # SQLite database (created automatically)
├── personas/
│   ├── riley-executive-function.json
│   ├── sam-daily-life.json
│   └── alex-relationships.json
└── public/
    ├── index.html            # Chat interface
    ├── style.css             # Styling
    └── app.js                # Frontend JavaScript
```

---

## Getting Started (Right Now)

**Step 1: Create project folder**
```bash
mkdir adhd-coach
cd adhd-coach
```

**Step 2: Tell Claude Code**
> "Let's build the minimal ADHD persona chat following the Bootstrap Guide. Day 1: Basic chat with Express, SQLite, one ADHD persona (Executive Function Coach). Use bootstrap approach - simple, local, no auth."

**Step 3: Follow along**
- Claude Code writes the code
- You test each piece
- Iterate until it works

**Step 4: Customize**
- Edit persona prompts based on your needs
- Add/remove personas
- Adjust UI to your preferences

---

## FAQ

### Q: Can I use this on my phone?
**A:** Not with bootstrap version (localhost only). To use on phone, you'd need to deploy (Vercel free tier works, but adds complexity).

### Q: What if I exceed Claude API free trial?
**A:** There's no free tier for Claude API - you pay per use from day 1. But personal use is cheap ($5-15/month).

### Q: Can I share this with my partner/friends?
**A:** Technically yes (they can access localhost on your network), but it's not designed for multi-user. For sharing, consider full build with auth.

### Q: Is my data private?
**A:** Yes, with bootstrap version everything is local (SQLite file on your computer). Only Claude API sees messages (and Anthropic doesn't train on API data).

### Q: Can I use a different LLM (cheaper)?
**A:** Yes! You could use:
- **GPT-3.5-turbo**: Cheaper ($0.0005/1k input, $0.0015/1k output) = $1-3/month
- **Local LLM** (Ollama, LM Studio): Free, but lower quality for nuanced coaching
- **Claude Sonnet 3.5**: Best quality for ADHD coaching (worth the $5-15/month)

### Q: How do I back up my conversations?
**A:** Copy `conversations.db` file to backup location (Dropbox, Google Drive, external drive). It's just a file.

### Q: Can I export conversations?
**A:** Add export feature (Day 5 polish) or just query SQLite directly:
```sql
SELECT * FROM messages WHERE conversation_id = 'xxx';
```

---

## Next Steps

**Ready to start?**

1. Choose your path:
   - **Super minimal (1 day)**: Just test the concept
   - **Bootstrap (3-5 days)**: Fully functional personal tool
   - **Full build (14 days)**: Production-ready for sharing

2. Define your personas:
   - Use `personas/adhd-personas-starter.md` as starting point
   - Customize based on your specific ADHD needs
   - Start with 1-2 personas, add more later

3. Tell Claude Code to begin:
   - Copy the "Tell Claude Code" prompts from above
   - Let Claude Code write the code
   - You test and provide feedback

**Ready when you are!**

---

**Version:** 1.0 (Bootstrap Guide)
**Last Updated:** 2025-10-26
**Target Cost:** $5-20/month for personal use
**Build Time:** 1-5 days depending on features
