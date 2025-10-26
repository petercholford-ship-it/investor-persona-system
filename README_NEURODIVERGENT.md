# Neurodivergent Support Personas - ADHD Focus

**Branch:** `neurodivergent-personas`
**Status:** Research & Development Phase
**Target:** Personal use, bootstrap build for minimal cost

---

## Overview

This branch contains ADHD support personas designed to help neurodivergent individuals (starting with ADHD) navigate daily challenges with compassionate, practical AI coaching.

**Key Principles:**
- ✅ Compassion over criticism (ADHD shame is real)
- ✅ Concrete over abstract ("Start at 2pm" not "start soon")
- ✅ Systems over willpower (ADHD willpower is unreliable)
- ✅ Validation first, then action
- ✅ Short, scannable messages (wall of text = executive dysfunction)

---

## 📋 Starter Personas (4 ADHD Coaches)

### 1. **Riley** - Executive Function Coach
**Focus:** Task initiation, organization, time management, follow-through

**Best for:**
- Getting unstuck when paralyzed by overwhelm
- Breaking down large tasks into micro-steps
- Managing time blindness
- Accountability and structure

**Example interaction:** [See personas/adhd-personas-starter.md]

---

### 2. **Jordan** - ADHD Career Navigator
**Focus:** Career guidance, workplace accommodations, job fit

**Best for:**
- Finding ADHD-compatible careers
- Requesting workplace accommodations
- Navigating disclosure decisions
- Career pivots and non-linear paths

**Example interaction:** [See personas/adhd-personas-starter.md]

---

### 3. **Alex** - Relationship & Communication Coach
**Focus:** Relationships, communication, rejection sensitivity

**Best for:**
- Time blindness in relationships
- Emotional dysregulation and repair
- Rejection Sensitive Dysphoria (RSD)
- Explaining ADHD to partners/friends

**Example interaction:** [See personas/adhd-personas-starter.md]

---

### 4. **Sam** - Daily Life & Routine Coach
**Focus:** Daily routines, habits, ADLs (activities of daily living)

**Best for:**
- Morning/evening routines
- Cleaning and organization
- Meal planning (ADHD meals)
- Sleep hygiene
- Errand paralysis

**Example interaction:** [See personas/adhd-personas-starter.md]

---

## 🚀 Two Build Options

### Option A: Bootstrap (Personal Use)
**Goal:** Get running for < $10-20/month

**Approach:**
- Simple Express.js + SQLite
- Run locally (no deployment)
- 1-5 day build time
- Perfect for personal ADHD support

**See:** [docs/BOOTSTRAP_GUIDE.md](docs/BOOTSTRAP_GUIDE.md)

**Costs:**
- Claude API: $5-15/month (only real cost)
- Everything else: FREE (local SQLite, no hosting)

---

### Option B: Full Build (Production)
**Goal:** Share with others, mobile access, better memory

**Approach:**
- Full Next.js + Supabase + Vercel
- Follow 14-day build plan
- Multi-user support
- RAG memory with embeddings

**See:** [docs/persona-system-build-guide.md](docs/persona-system-build-guide.md)

**Costs:**
- Development: 3-4 weeks
- Monthly: $105-565 depending on usage

---

## 📖 Documentation

### Getting Started
1. **[BOOTSTRAP_GUIDE.md](docs/BOOTSTRAP_GUIDE.md)** - Build for personal use, minimal cost
2. **[adhd-personas-starter.md](personas/adhd-personas-starter.md)** - 4 ADHD persona templates + research questions

### Research Phase (Do This First!)
Before building, research to customize personas:

**Questions to explore:**
- What specific ADHD challenges cause the most friction?
- What existing strategies work best? (body doubling, timers, visual cues)
- What language feels supportive vs. patronizing?
- How to balance structure with flexibility?

**Resources to review:**
- r/ADHD top posts (pain points)
- How to ADHD (YouTube - Jessica McCabe)
- Driven to Distraction (book - Edward Hallowell)
- ADHD 2.0 (book - Hallowell & Ratey)
- Russell Barkley's ADHD research (evidence-based strategies)

**Consider interviewing:**
- ADHD adults about daily struggles
- ADHD coaches/therapists
- People using ADHD support tools

---

## 💡 Why This Approach?

### Advantages Over Existing ADHD Tools
- **More personalized**: Remembers your specific challenges and strategies
- **Always available**: 3am executive dysfunction? Persona is there.
- **No judgment**: AI doesn't get frustrated or disappointed
- **Cheaper than coaching**: $10-15/month vs. $100-300/session
- **Multiple specialties**: Switch between executive function, relationships, career, daily life

### Disadvantages
- **Not therapy**: This is coaching support, not mental health treatment
- **Not medical**: Can't diagnose, prescribe medication, or replace psychiatrist
- **Requires internet**: Needs Claude API access
- **AI limitations**: May not understand nuanced situations

---

## 🎯 Quick Start (Bootstrap)

### Prerequisites
- Node.js 18+
- Anthropic API key ([get one here](https://console.anthropic.com/))
- 2-3 hours for minimal build

### Step 1: Tell Claude Code
```
"Let's build the minimal ADHD persona chat following the Bootstrap Guide.
Use Express.js, SQLite, one ADHD persona (Executive Function Coach Riley).
Simple, local, no auth, bootstrap approach."
```

### Step 2: Customize Persona
Edit persona prompt based on your specific ADHD challenges:
- Which ADHD symptoms affect you most?
- What communication style helps you?
- What strategies have worked for you before?

### Step 3: Test & Iterate
- Use it for a week
- Note what's helpful vs. annoying
- Adjust persona prompt
- Add/remove personas as needed

---

## 📊 Cost Comparison

### Option 1: Bootstrap (Personal)
```
Monthly Cost: $5-15
- Claude API: $5-15 (only cost)
- SQLite: Free (local database)
- Hosting: Free (run locally)

Build Time: 1-5 days
Complexity: Low
```

### Option 2: Full Build (Production)
```
Monthly Cost: $100-500
- Claude API: $50-300
- Supabase: $25
- Vercel: $20
- OpenAI Embeddings: $5-20

Build Time: 3-4 weeks
Complexity: Medium-High
```

### Option 3: Use Existing Tool
```
Monthly Cost: $20-200
- ChatGPT Plus + Custom GPTs: $20/month
- ADHD Coaching Apps: $50-100/month
- Human ADHD Coach: $400-1200/month

Pros: Ready to use
Cons: Not personalized, no memory, generic advice
```

**Recommendation for personal use:** Bootstrap version ($5-15/month)

---

## 🔬 Research Roadmap

### Phase 1: Initial Research (1-2 weeks)
- [ ] Review r/ADHD top 100 posts (identify pain points)
- [ ] Watch How to ADHD YouTube series
- [ ] Read Driven to Distraction (foundational ADHD book)
- [ ] Interview 3-5 ADHD adults (what would help most?)
- [ ] Review Russell Barkley's strategies (evidence-based)

### Phase 2: Persona Refinement (1 week)
- [ ] Choose 2-3 personas to focus on (based on research)
- [ ] Write detailed system prompts (500-800 tokens each)
- [ ] Create 5+ example interactions per persona
- [ ] Test tone/language with ADHD individuals

### Phase 3: Personal Customization (1 week)
- [ ] Add your specific ADHD challenges
- [ ] Include strategies that work for you
- [ ] Adjust communication style to your preferences
- [ ] Test and iterate based on real usage

### Phase 4: Build (1-5 days)
- [ ] Follow bootstrap guide
- [ ] Implement chosen personas
- [ ] Test thoroughly
- [ ] Refine based on use

---

## ⚠️ Important Notes

### This is NOT:
- ❌ Medical advice or therapy
- ❌ Replacement for ADHD medication
- ❌ Diagnostic tool
- ❌ Crisis intervention

### This IS:
- ✅ Supportive coaching for daily challenges
- ✅ Accountability and structure
- ✅ ADHD-aware guidance
- ✅ Compassionate support 24/7

### Always Recommend Professional Help For:
- Crisis situations (suicidal thoughts, severe depression)
- Medication management (see psychiatrist)
- Diagnosis (see ADHD specialist)
- Therapy needs (trauma, severe anxiety, etc.)

---

## 🎨 Example Use Cases

### Morning Routine Paralysis
**You:** "I've been sitting on the edge of my bed for 45 minutes. I need to shower and get to work but I can't start."

**Sam (Daily Life Coach):** "Okay, that's classic task initiation paralysis. Not a character flaw - your brain needs a smaller first step.

Don't think about 'shower and get ready.' Too big.

**Just answer this:** Can you stand up and walk to the bathroom? Not shower, just walk there.

Yes or no?"

---

### Work Task Overwhelm
**You:** "I have a huge project due Friday and I haven't started. I keep opening the doc and then closing it. I'm going to fail."

**Riley (Executive Function Coach):** "You're not going to fail. You're experiencing executive dysfunction, which is ADHD, not incompetence.

Let's break this down:

**Don't think about:** 'Finish project'
**Think about:** 'Open doc and write one sentence'

That's it. One sentence. Anything. It can suck. Just one sentence.

Can you do that in the next 5 minutes?

(And set a timer for 10 minutes after - hyperfocus trap is real)"

---

### Relationship Conflict
**You:** "My partner is mad because I forgot to pay the electric bill again. I feel like I'm constantly disappointing them."

**Alex (Relationship Coach):** "Time blindness and task initiation are real ADHD challenges. Your partner's frustration is valid AND this isn't a character flaw.

Two things to do:

**Right now:**
1. Pay the bill (if you can)
2. Apologize: 'I'm sorry I forgot. I know this is frustrating. I'm setting up autopay right now so it doesn't happen again.'

**System fix:**
- Set up autopay for ALL recurring bills
- Not relying on memory = working with your ADHD brain

This is solving the problem, not making excuses. There's a difference."

---

## 📚 Additional Resources

### ADHD Communities
- r/ADHD (Reddit)
- r/ADHDwomen (Reddit)
- How to ADHD (YouTube)
- ADHD Nerds (Podcast)

### Books
- *Driven to Distraction* - Edward Hallowell & John Ratey
- *ADHD 2.0* - Hallowell & Ratey (updated strategies)
- *Scattered Minds* - Gabor Maté
- *The ADHD Effect on Marriage* - Melissa Orlov

### Professional Organizations
- CHADD (Children and Adults with ADHD)
- ADDA (Attention Deficit Disorder Association)
- ACO (ADHD Coaches Organization)

---

## 🤝 Contributing

**Have ADHD and ideas for personas?**
- Open an issue with persona suggestions
- Share what's helpful vs. what's not
- Contribute example interactions
- Help test and refine

**Research findings to share?**
- Effective ADHD strategies
- Common pain points
- Language that helps vs. hurts
- Evidence-based interventions

---

## 📝 License

[To be determined]

---

## 🔗 Links

- **Bootstrap Guide**: [docs/BOOTSTRAP_GUIDE.md](docs/BOOTSTRAP_GUIDE.md)
- **ADHD Personas**: [personas/adhd-personas-starter.md](personas/adhd-personas-starter.md)
- **Generic Build Guide**: [docs/persona-system-build-guide.md](docs/persona-system-build-guide.md)
- **Main Branch** (generic infrastructure): [README.md](README.md)

---

**Built for neurodivergent individuals, by someone who gets it**

**Last Updated:** 2025-10-26
**Status:** Research & Development - Ready for personal bootstrap build
