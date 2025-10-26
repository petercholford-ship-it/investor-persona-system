# Investor Persona System

AI-powered conversational assistants for professional investors, featuring specialized personas for VC Partners, Angel Investors, Hedge Fund Analysts, and PE Associates.

## 🎯 Overview

This is a greenfield project to build a multi-persona AI chat system specifically designed for sophisticated investors. Each persona embodies deep domain expertise, investment frameworks, and communication styles tailored to different investment strategies.

**Status:** Not Started (Build templates ready)

## ✨ Key Features

- **4 Specialized Investor Personas**
  - VC Partner (early-stage, Series A/B focus)
  - Angel Investor (seed/pre-seed, founder-focused)
  - Hedge Fund Analyst (public markets, quantitative)
  - PE Associate (buyouts, operational improvement)

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

## 📋 Build Plan

This project follows a **14-day build plan** designed for implementation via Claude Code assistance:

### Phase 1: Foundation (Week 1)
- Day 1: Project setup (Next.js, TypeScript, Tailwind)
- Day 2: Supabase database schema with pgvector
- Day 3: Authentication (Supabase Auth)
- Day 4: Investor persona configuration

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

**Detailed Build Guide:** [docs/investor-persona-build-guide.md](docs/investor-persona-build-guide.md)

## 📖 Documentation

- **[Build Guide](docs/investor-persona-build-guide.md)** - Complete 14-day implementation guide
- **[Phase 1 Status](templates/PHASE_1_STATUS.md)** - Foundation tracking
- **[Phase 2 Status](templates/PHASE_2_STATUS.md)** - Core Chat tracking
- **[Phase 3 Status](templates/PHASE_3_STATUS.md)** - Usage & Permissions tracking
- **[Phase 4 Status](templates/PHASE_4_STATUS.md)** - Polish & Deploy tracking
- **[Current Phase](templates/CURRENT_PHASE.md)** - Overall progress tracker

## 🚀 Getting Started

### Prerequisites

- Node.js 18+
- Git
- GitHub account
- Anthropic API key ([get one here](https://console.anthropic.com/))
- Supabase account ([sign up](https://supabase.com/))
- Upstash account ([sign up](https://upstash.com/))
- OpenAI API key ([get one here](https://platform.openai.com/))

### Quick Start (When Build Begins)

```bash
# Clone repository
git clone https://github.com/YOUR_USERNAME/investor-persona-system.git
cd investor-persona-system

# Install dependencies
npm install

# Set up environment variables
cp .env.example .env.local
# Edit .env.local with your API keys

# Run development server
npm run dev

# Open http://localhost:3000
```

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

1. **Review the Build Guide**: Read [docs/investor-persona-build-guide.md](docs/investor-persona-build-guide.md)

2. **Tell Claude Code to begin**:
   > "Let's start building the Investor Persona System. Begin with Phase 1, Day 1."

3. **Claude Code will**:
   - Write all code following the build guide
   - Create files and components
   - Set up database schemas
   - Implement features step-by-step

4. **Your role**:
   - Review and test each day's work
   - Add API keys to `.env.local`
   - Run verification checklists
   - Provide feedback on UX/design

5. **Track progress** in `templates/PHASE_*_STATUS.md` files

## 📊 Cost Estimates

### Development
- **Phase 1-3** (MVP): 3-4 weeks, $0 if DIY with Claude Code
- **Monthly Infrastructure**: $105-265 (development), $565-1565 (100 users)

### Primary Cost Driver: Claude API
- 80-90% of total operating costs
- ~$0.20-0.50 per investor conversation
- See [Build Guide](docs/investor-persona-build-guide.md#cost-breakdown--drivers) for detailed breakdown

## 🎨 Features Roadmap

### Phase 1-4 (MVP)
- ✅ Core chat with 4 investor personas
- ✅ RAG-based long-term memory
- ✅ Tiered access and quotas
- ✅ Admin panel
- ✅ Production deployment

### Future Phases
- **Phase 5**: Custom personas, integrations, collaboration
- **Phase 6**: Proactive insights, multi-agent debates, voice interface
- **Phase 7**: API-first with SDKs

## 🤝 Contributing

This is currently a personal project. Contributions welcome after MVP launch.

## 📝 License

[To be determined]

## 🙋 Support

- **Build Issues**: Check [.claude/instructions.md](.claude/instructions.md)
- **Feature Requests**: Open an issue (after repo is public)

## 🔗 Links

- **Build Guide**: [docs/investor-persona-build-guide.md](docs/investor-persona-build-guide.md)
- **Market Research**: See build guide for comparators (Character.AI, CustomGPT, Digital Ray)
- **Tech Stack Docs**:
  - [Next.js 14](https://nextjs.org/docs)
  - [Supabase](https://supabase.com/docs)
  - [Claude API](https://docs.anthropic.com/)
  - [Upstash Redis](https://docs.upstash.com/redis)

---

**Built with Claude Code** | **Last Updated:** 2025-10-26
