# Phase 2: Core Chat (Week 2) - Status

**Duration:** Days 5-7
**Status:** Not Started
**Started:** N/A
**Completed:** N/A

---

## Overview

Phase 2 implements the core chat functionality: UI, Claude API integration with streaming, and RAG-based memory system.

---

## Day 5: Chat Interface

**Status:** Not Started
**Estimated Time:** 4-5 hours
**Actual Time:** N/A

### Tasks

- [ ] Create chat page with conversation routing
- [ ] Build persona selector dropdown
- [ ] Implement conversation history sidebar
- [ ] Create message display components
- [ ] Build chat input component with auto-resize
- [ ] Add usage indicator showing messages remaining

### Claude Code Creates

- [ ] `/app/chat/[id]/page.tsx` (chat UI)
- [ ] `/components/ChatMessage.tsx` (message display)
- [ ] `/components/ChatInput.tsx` (input with auto-resize)
- [ ] `/components/PersonaSelector.tsx` (dropdown)
- [ ] `/components/ConversationSidebar.tsx` (history list)
- [ ] `/components/UsageIndicator.tsx` (quota display)

### Your Checklist

- [ ] Navigate to `/chat/new`
- [ ] Test UI layout (no functionality yet)
- [ ] Verify responsive design (mobile + desktop)
- [ ] Provide feedback on design to Claude
- [ ] Iterate until satisfied

### Verification

- [ ] Clean, professional design
- [ ] Responsive (works on mobile)
- [ ] All components visible:
  - [ ] Persona selector at top
  - [ ] Conversation sidebar on left
  - [ ] Main chat area in center
  - [ ] Input box at bottom
  - [ ] Usage indicator visible
- [ ] Proper spacing and typography
- [ ] Loading states for future async operations

### UI Checklist

- [ ] Message bubbles (user vs assistant styling)
- [ ] Timestamp on each message
- [ ] Persona avatar/icon in assistant messages
- [ ] Auto-scroll to bottom on new messages
- [ ] Input textarea expands with content
- [ ] Send button (with keyboard shortcut hint)

### Notes

<!-- Design feedback, UI iterations, accessibility considerations -->

---

## Day 6: Claude API Integration

**Status:** Not Started
**Estimated Time:** 4-5 hours
**Actual Time:** N/A

### Tasks

- [ ] Create Claude API client wrapper
- [ ] Implement streaming response handler
- [ ] Build `/api/chat` endpoint
- [ ] Add token counting and usage tracking
- [ ] Store messages in database
- [ ] Handle errors gracefully

### Claude Code Creates

- [ ] `/app/api/chat/route.ts` (streaming endpoint)
- [ ] `/lib/claude/client.ts` (Claude API wrapper)
- [ ] `/lib/claude/streaming.ts` (streaming handler)
- [ ] `/lib/usage-tracker.ts` (token counting)
- [ ] `/lib/error-handler.ts` (error handling)

### Your Checklist

- [ ] Add `CLAUDE_API_KEY` to `.env.local`
- [ ] Test sending a message in chat UI
- [ ] Verify streaming response appears character-by-character
- [ ] Check Supabase: messages saved in database
- [ ] Check token counts recorded

### Verification

- [ ] Can send message and get response
- [ ] Response streams in real-time (not all at once)
- [ ] User message saved to database immediately
- [ ] Assistant response saved after completion
- [ ] Token count accurate (use Claude's tokenizer)
- [ ] Error handling works (test with invalid API key)
- [ ] Rate limiting prevents abuse

### API Integration Checklist

**Request Flow:**
- [ ] Load conversation history from database
- [ ] Load persona system prompt
- [ ] Build context (system + history + new message)
- [ ] Send to Claude API
- [ ] Stream response to frontend
- [ ] Save to database on completion

**Error Handling:**
- [ ] Invalid API key → clear error message
- [ ] Rate limit exceeded → helpful message with wait time
- [ ] Network timeout → retry logic
- [ ] Malformed response → log and show generic error

**Token Management:**
- [ ] Count input tokens (system + history + user message)
- [ ] Count output tokens (assistant response)
- [ ] Store in messages table
- [ ] Update user's usage quota
- [ ] Show accurate token counts in admin panel

### Notes

<!-- API configuration, token optimization strategies, errors encountered -->

---

## Day 7: Memory System (RAG)

**Status:** Not Started
**Estimated Time:** 5-6 hours
**Actual Time:** N/A

### Tasks

- [ ] Implement conversation summarization (every 10 messages)
- [ ] Add OpenAI embedding generation
- [ ] Create vector storage in pgvector
- [ ] Build semantic search for memory retrieval
- [ ] Inject retrieved memories into Claude context
- [ ] Test memory across multiple conversations

### Claude Code Creates

- [ ] `/lib/memory/summarizer.ts` (conversation summarization)
- [ ] `/lib/memory/embeddings.ts` (OpenAI embedding generation)
- [ ] `/lib/memory/retrieval.ts` (vector similarity search)
- [ ] `/lib/memory/storage.ts` (pgvector operations)
- [ ] `/app/api/chat/route.ts` (updated with memory injection)

### Your Checklist

- [ ] Add `OPENAI_API_KEY` to `.env.local`
- [ ] Have a 10+ message conversation
- [ ] Check Supabase: `conversation_memories` table has entries
- [ ] Start new conversation, reference something from old conversation
- [ ] Claude should "remember" the context

### Verification

**Summarization:**
- [ ] Summaries created after every 10 messages
- [ ] Summaries are concise (100-200 tokens)
- [ ] Summaries capture key points (companies, decisions, insights)

**Embeddings:**
- [ ] Vectors stored in `conversation_memories` table
- [ ] Embedding dimension = 384 (text-embedding-3-small)
- [ ] Vector index created for fast similarity search

**Retrieval:**
- [ ] Semantic search finds relevant past conversations
- [ ] Top 5 most relevant memories retrieved
- [ ] Irrelevant memories not retrieved (good filtering)
- [ ] Search latency < 100ms

**Integration:**
- [ ] Retrieved memories injected into Claude context
- [ ] Claude references past context accurately
- [ ] Memory retrieval transparent to user
- [ ] Works across multiple days/sessions

### RAG Workflow Checklist

**Storage (after 10 messages):**
- [ ] Summarize conversation chunk
- [ ] Generate embedding vector
- [ ] Store in `conversation_memories` with metadata
- [ ] Log success/failure

**Retrieval (on new message):**
- [ ] Generate embedding for user's message
- [ ] Vector similarity search in pgvector
- [ ] Retrieve top 5 relevant memories
- [ ] Filter by user_id (privacy)
- [ ] Inject into Claude context

**Context Assembly:**
```
[System Prompt: Investor Persona]
[Retrieved Memories: Top 5 relevant past conversations]
[Recent History: Last 10 messages]
[New User Message]
```

### Testing Scenarios

Test memory across these scenarios:
- [ ] **Continuity:** Reference specific company discussed 2 days ago
- [ ] **Synthesis:** Ask about portfolio composition (aggregate past conversations)
- [ ] **Clarification:** Claude reminds you of previous analysis
- [ ] **Privacy:** User A's memories not visible to User B

### Notes

<!-- Memory configuration, embedding costs, retrieval accuracy, improvements needed -->

---

## Phase 2 Summary

**Days Completed:** 0 / 3
**Overall Status:** Not Started

**Key Features Delivered:**
- [ ] Chat interface with streaming
- [ ] Claude API integration
- [ ] Long-term memory (RAG)

**Blockers:** None

**Next Phase:** Phase 3 - Usage & Permissions (Days 8-10)

---

## Sign-Off

**Phase 2 Complete?** [ ] Yes / [ ] No

**Ready for Phase 3?** [ ] Yes / [ ] No

**Notes:**
<!-- Overall phase notes, memory performance, UX feedback -->
