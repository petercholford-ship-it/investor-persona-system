# Multi-Persona AI System

A generic, extensible framework for building AI conversational assistants with specialized personas across any domain.

## 🎯 Overview

This is a **reusable infrastructure** for creating multi-persona AI chat systems. The framework is domain-agnostic - use it to build specialized assistants for investors, coaches, technical advisors, therapists, or any other domain.

**Repository Structure:**
- **`main` branch**: Generic infrastructure and build framework (you are here)
- **`investor-personas` branch**: Example implementation with 4 investor personas (VC, Angel, Hedge Fund, PE)
- **Create your own branch**: Use the template to build personas for your domain

## ✨ Core Features

- **Multi-Persona Support**
  - Switch between specialized personas in one conversation platform
  - Each persona has unique expertise, frameworks, and communication styles
  - Easily extendable to any number of personas

- **Long-Term Memory (RAG)**
  - Semantic search using pgvector
  - Remembers conversations across sessions
  - Context-aware responses referencing past discussions

- **Tiered Access & Quotas**
  - Free: 10 messages/month
  - Starter: 100 messages/month
  - Professional: 500 messages/month
  - Enterprise: Unlimited

- **Professional Features**
  - Streaming responses for real-time interaction
  - Conversation export (PDF, Markdown)
  - Admin panel for user management
  - Rate limiting and abuse prevention
  - Mobile responsive and accessible (WCAG AA)

## 🏗️ Architecture

**Stack:**
- **Frontend**: Next.js 14 (App Router), TypeScript, Tailwind CSS
- **Backend**: Next.js API Routes (serverless)
- **Database**: Supabase (PostgreSQL + Auth + pgvector)
- **Cache**: Upstash Redis
- **AI**: Claude 3.5 Sonnet (Anthropic)
- **Embeddings**: OpenAI text-embedding-3-small
- **Deployment**: Vercel

**Memory Architecture:**
- **Short-term**: Redis (session, last 10 messages)
- **Medium-term**: PostgreSQL (90-day conversation history)
- **Long-term**: pgvector (semantic RAG, indefinite)

## 🌿 Branch Structure

### **`main`** (Generic Infrastructure)
The foundation that works for any persona type:
- Generic build system and templates
- Infrastructure code (auth, database, API)
- Persona template and creation guide
- Deployment configuration

### **`investor-personas`** (Example Implementation)
A complete example with 4 investor personas:
- VC Partner (early-stage investing)
- Angel Investor (seed/pre-seed)
- Hedge Fund Analyst (public markets)
- PE Associate (buyouts)

**See:** [investor-personas branch](https://github.com/petercholford-ship-it/investor-persona-system/tree/investor-personas)

### **Create Your Own Branch**
Want to build coach personas? Technical advisor personas? Do this:

```bash
# Clone the repo
git clone https://github.com/petercholford-ship-it/investor-persona-system.git

# Create your domain branch
git checkout -b coach-personas  # or technical-personas, therapist-personas, etc.

# Use the persona template
cp templates/PERSONA_TEMPLATE.md personas/my-first-persona.md

# Define your personas following the template
# Build using the generic infrastructure on main
```

## 📖 Documentation

- **[Persona Template](templates/PERSONA_TEMPLATE.md)** - How to define any persona type
- **[Build Guide](docs/persona-system-build-guide.md)** - Complete 14-day implementation guide
- **[Phase Trackers](templates/)** - Progress tracking templates

**Example Implementation:**
- See [`investor-personas`](https://github.com/petercholford-ship-it/investor-persona-system/tree/investor-personas) branch for a complete example

## 🚀 Getting Started

### Option 1: Use Existing Persona Set (e.g., Investor Personas)

```bash
# Clone and switch to example branch
git clone https://github.com/petercholford-ship-it/investor-persona-system.git
cd investor-persona-system
git checkout investor-personas

# Follow the build guide in that branch
npm install
# ... (see investor-personas README)
```

### Option 2: Build Your Own Persona Set

```bash
# Clone the generic infrastructure
git clone https://github.com/petercholford-ship-it/investor-persona-system.git
cd investor-persona-system

# Stay on main branch (or create your own)
git checkout -b my-domain-personas

# Define your personas using the template
cp templates/PERSONA_TEMPLATE.md personas/persona-1.md

# Edit personas/persona-1.md with your persona definition
# Follow the build guide to implement
```

## 📋 Build Plan (14 Days)

This project follows a **14-day build plan** designed for implementation via Claude Code assistance:

### Phase 1: Foundation (Week 1)
- Day 1: Project setup (Next.js, TypeScript, Tailwind)
- Day 2: Supabase database schema with pgvector
- Day 3: Authentication (Supabase Auth)
- Day 4: Persona configuration (using your defined personas)

### Phase 2: Core Chat (Week 2)
- Day 5: Chat interface with streaming
- Day 6: Claude API integration
- Day 7: RAG memory system

### Phase 3: Usage & Permissions (Week 3)
- Day 8: User tiers and quotas
- Day 9: Admin panel
- Day 10: Rate limiting

### Phase 4: Polish & Deploy (Week 4)
- Day 11: Error handling and loading states
- Day 12: Conversation management
- Day 13: Mobile responsive and accessibility
- Day 14: Documentation and deployment

**Detailed Build Guide:** [docs/persona-system-build-guide.md](docs/persona-system-build-guide.md)

## 🔧 Environment Variables

Create a `.env.local` file with:

```bash
# Supabase
NEXT_PUBLIC_SUPABASE_URL=your_supabase_url
NEXT_PUBLIC_SUPABASE_ANON_KEY=your_supabase_anon_key
SUPABASE_SERVICE_ROLE_KEY=your_supabase_service_role_key

# Claude AI
CLAUDE_API_KEY=your_anthropic_api_key

# OpenAI (for embeddings)
OPENAI_API_KEY=your_openai_api_key

# Upstash Redis
UPSTASH_REDIS_REST_URL=your_upstash_redis_url
UPSTASH_REDIS_REST_TOKEN=your_upstash_redis_token

# App Configuration
NEXT_PUBLIC_APP_URL=http://localhost:3000
```

## 💡 How to Build This Project

This project is designed for **automated building via Claude Code**:

1. **Define Your Personas**: Use [templates/PERSONA_TEMPLATE.md](templates/PERSONA_TEMPLATE.md) to create 2-4 personas for your domain

2. **Tell Claude Code to begin**:
   > "Let's start building the [Your Domain] Persona System. I've defined 3 personas in the personas/ folder. Begin with Phase 1, Day 1."

3. **Claude Code will**:
   - Write all code following the build guide
   - Create files and components
   - Set up database schemas
   - Implement features step-by-step

4. **Your role**:
   - Define personas (domain expertise required)
   - Add API keys to `.env.local`
   - Review and test each day's work
   - Provide feedback on UX/design

5. **Track progress** in `templates/PHASE_*_STATUS.md` files

## 📊 Cost Estimates

### Development
- **Phase 1-3** (MVP): 3-4 weeks, $0 if DIY with Claude Code
- **Monthly Infrastructure**: $105-265 (development), $565-1565 (100 users)

### Primary Cost Driver: Claude API
- 80-90% of total operating costs
- ~$0.20-0.50 per conversation (varies by persona complexity)
- See [Build Guide](docs/persona-system-build-guide.md) for detailed breakdown

## 🎨 Example Use Cases

This infrastructure can power persona systems for:

### Professional Services
- **Investment Advisory**: VC, Angel, Hedge Fund, PE personas
- **Career Coaching**: Executive coach, career counselor, interview prep
- **Technical Consulting**: Architect, DevOps, security specialist
- **Business Strategy**: Product manager, growth marketer, operations expert

### Personal Development
- **Wellness**: Therapist, nutritionist, fitness coach
- **Creative**: Writer, designer, creative strategist
- **Academic**: Tutor, research advisor, writing coach

### Domain-Specific
- **Legal**: Corporate lawyer, IP specialist, contract reviewer
- **Medical**: Primary care, specialist, mental health (disclaimer: not for diagnosis)
- **Real Estate**: Buyer's agent, seller's agent, investor advisor

## 🌟 Example Implementations

### Investor Personas (Complete)
See [`investor-personas` branch](https://github.com/petercholford-ship-it/investor-persona-system/tree/investor-personas) for:
- 4 fully-defined investor personas
- Domain-specific build guide
- Example conversations
- Market analysis and positioning

**Personas:**
1. VC Partner (Morgan) - Early-stage focus, Series A/B expertise
2. Angel Investor (Alex) - Seed/pre-seed, founder-focused
3. Hedge Fund Analyst (Jordan) - Public markets, quantitative
4. PE Associate (Taylor) - Buyouts, operational improvement

### Your Implementation Here
Create your own branch and link it here:
- **[Your Domain] Personas** - [Link to your branch]

## 🤝 Contributing

Contributions welcome! To add a new persona domain:

1. Fork this repository
2. Create a new branch: `git checkout -b [domain]-personas`
3. Define 2-4 personas using the template
4. Customize build guide for your domain
5. Submit a pull request

## 📝 License

[To be determined]

## 🔗 Links

- **Generic Build Guide**: [docs/persona-system-build-guide.md](docs/persona-system-build-guide.md)
- **Persona Template**: [templates/PERSONA_TEMPLATE.md](templates/PERSONA_TEMPLATE.md)
- **Example Implementation**: [investor-personas branch](https://github.com/petercholford-ship-it/investor-persona-system/tree/investor-personas)
- **Tech Stack Docs**:
  - [Next.js 14](https://nextjs.org/docs)
  - [Supabase](https://supabase.com/docs)
  - [Claude API](https://docs.anthropic.com/)
  - [Upstash Redis](https://docs.upstash.com/redis)

---

**Built with Claude Code** | **Last Updated:** 2025-10-26

**Repository:** https://github.com/petercholford-ship-it/investor-persona-system
