# Phase 3: Usage & Permissions (Week 3) - Status

**Duration:** Days 8-10
**Status:** Not Started
**Started:** N/A
**Completed:** N/A

---

## Overview

Phase 3 implements user tiers, usage quotas, admin panel for user management, and rate limiting.

---

## Day 8: User Tiers & Quotas

**Status:** Not Started
**Estimated Time:** 3-4 hours
**Actual Time:** N/A

### Tasks

- [ ] Add `tier` column to users table
- [ ] Implement quota system (free, starter, professional, enterprise)
- [ ] Build quota checker middleware
- [ ] Create usage tracking API
- [ ] Display usage remaining in UI
- [ ] Block messages when quota exceeded

### Claude Code Creates

- [ ] `/supabase/migrations/002_add_user_tiers.sql`
- [ ] `/lib/quota/checker.ts` (quota enforcement)
- [ ] `/lib/quota/types.ts` (tier definitions)
- [ ] `/app/api/usage/route.ts` (GET current usage)
- [ ] `/components/UsageIndicator.tsx` (enhanced with quota)

### Your Checklist

- [ ] Run migration in Supabase SQL Editor
- [ ] Set your user tier to "professional" in Supabase dashboard
- [ ] Send messages and watch usage counter decrease
- [ ] Manually set quota to 0 in database
- [ ] Verify blocking works (clear error message)

### Verification

**Tier Configuration:**
- [ ] Free tier: 10 messages/month
- [ ] Starter tier: 100 messages/month
- [ ] Professional tier: 500 messages/month
- [ ] Enterprise tier: Unlimited

**Quota Enforcement:**
- [ ] Usage indicator shows correct remaining count
- [ ] Counter decrements after each message
- [ ] Blocks new messages when quota = 0
- [ ] Clear error message: "You've used all X messages this month. Upgrade to continue."
- [ ] Error shows upgrade options with pricing

**Database Schema:**
- [ ] `users.tier` column (enum: free, starter, professional, enterprise)
- [ ] `users.messages_used_this_month` (integer)
- [ ] `users.quota_reset_date` (timestamp)
- [ ] Monthly quota reset logic (cron job or on-demand)

### Quota Logic Checklist

**On Message Send:**
- [ ] Check user's current tier
- [ ] Get tier's message limit
- [ ] Check `messages_used_this_month` vs limit
- [ ] If quota exceeded → return 429 error
- [ ] If quota available → increment counter
- [ ] Save updated count to database

**Monthly Reset:**
- [ ] Reset `messages_used_this_month` to 0
- [ ] Update `quota_reset_date` to next month
- [ ] Send email notification: "Your quota has reset"

### Notes

<!-- Tier definitions, quota logic, reset mechanism -->

---

## Day 9: Admin Panel

**Status:** Not Started
**Estimated Time:** 4-5 hours
**Actual Time:** N/A

### Tasks

- [ ] Create admin dashboard at `/admin`
- [ ] Build user management interface (list, edit tiers, reset quotas)
- [ ] Add system-wide statistics dashboard
- [ ] Implement conversation viewing (anonymized)
- [ ] Add cost estimation calculator
- [ ] Restrict access to `is_admin = true` users

### Claude Code Creates

- [ ] `/app/admin/page.tsx` (main dashboard)
- [ ] `/app/admin/users/page.tsx` (user management)
- [ ] `/app/admin/conversations/page.tsx` (conversation logs)
- [ ] `/app/admin/stats/page.tsx` (system statistics)
- [ ] `/app/api/admin/users/route.ts` (user CRUD)
- [ ] `/app/api/admin/stats/route.ts` (system stats)
- [ ] `/middleware.ts` (updated for admin-only routes)

### Your Checklist

- [ ] In Supabase, add `is_admin` column to users table
- [ ] Set your user to `is_admin = true`
- [ ] Access `/admin`
- [ ] Test user tier changes
- [ ] Review system stats
- [ ] Test as non-admin user (should see 403 error)

### Verification

**Access Control:**
- [ ] Admin users can access `/admin/*`
- [ ] Non-admin users get 403 Forbidden
- [ ] Clear error message for non-admin attempts
- [ ] Middleware protects all admin routes

**User Management:**
- [ ] List all users with:
  - Email
  - Tier
  - Messages used this month
  - Quota limit
  - Last active date
- [ ] Change user tier (dropdown)
- [ ] Reset user quota (button)
- [ ] View user's conversations (privacy-respecting)
- [ ] Search/filter users

**System Statistics:**
- [ ] Total users (by tier)
- [ ] Total conversations
- [ ] Total messages sent
- [ ] Total tokens used (input + output)
- [ ] Estimated monthly cost (based on token usage)
- [ ] Daily active users (DAU)
- [ ] Monthly active users (MAU)
- [ ] Average messages per user
- [ ] Average tokens per message

**Cost Estimation:**
```
Input tokens: X million × $0.003 = $Y
Output tokens: Z million × $0.015 = $W
Total API cost: $Y + $W = $Total
```

### Admin Dashboard Checklist

**Overview Cards:**
- [ ] Total users
- [ ] Active conversations today
- [ ] Messages sent today
- [ ] Estimated cost today

**Charts:**
- [ ] Users by tier (pie chart)
- [ ] Messages over time (line chart)
- [ ] Cost over time (line chart)
- [ ] Most active users (table)

**Quick Actions:**
- [ ] Create new user (with tier)
- [ ] Bulk tier upgrade
- [ ] Export usage data (CSV)
- [ ] View system logs

### Notes

<!-- Admin panel feedback, stats accuracy, missing features -->

---

## Day 10: Rate Limiting

**Status:** Not Started
**Estimated Time:** 2-3 hours
**Actual Time:** N/A

### Tasks

- [ ] Set up Upstash Redis
- [ ] Implement Redis-based rate limiter
- [ ] Add per-user message rate limiting (10 messages/minute)
- [ ] Add IP-based rate limiting for login (5 attempts/15 minutes)
- [ ] Show clear error messages when rate limited
- [ ] Add rate limit headers to responses

### Claude Code Creates

- [ ] `/lib/rate-limiter.ts` (Redis-based rate limiting)
- [ ] `/lib/rate-limiter/user.ts` (per-user limits)
- [ ] `/lib/rate-limiter/ip.ts` (per-IP limits)
- [ ] `/app/api/chat/route.ts` (updated with rate check)
- [ ] `/app/api/auth/login/route.ts` (login rate limiting)

### Your Checklist

- [ ] Create Upstash Redis database (free tier)
- [ ] Add Redis URL and token to `.env.local`
- [ ] Test sending >10 messages rapidly
- [ ] Verify rate limit error appears
- [ ] Wait 1 minute, verify rate limit clears
- [ ] Test login brute force protection

### Verification

**User Rate Limiting:**
- [ ] Limit: 10 messages per minute per user
- [ ] Rate limit activates after 10th message
- [ ] Clear error: "Too many messages. Please wait 60 seconds."
- [ ] Shows countdown timer: "Try again in 45 seconds"
- [ ] Rate limit resets after 60 seconds
- [ ] Redis key expires automatically

**Login Rate Limiting:**
- [ ] Limit: 5 login attempts per 15 minutes per IP
- [ ] Activates after 5th failed login
- [ ] Error: "Too many login attempts. Try again in 15 minutes."
- [ ] Successful login resets counter
- [ ] Works across multiple users from same IP

**Response Headers:**
```
X-RateLimit-Limit: 10
X-RateLimit-Remaining: 7
X-RateLimit-Reset: 1234567890
```

**Rate Limiter Architecture:**
- [ ] Redis stores: `rate_limit:user:{userId}:messages` (TTL: 60s)
- [ ] Redis stores: `rate_limit:ip:{ip}:login` (TTL: 900s)
- [ ] Atomic increment operations (INCR)
- [ ] Automatic expiration (EXPIRE)
- [ ] Fast (<10ms per check)

### Testing Scenarios

**Message Rate Limit:**
- [ ] Send 10 messages quickly → 11th blocked
- [ ] Wait 60 seconds → can send again
- [ ] Multiple users don't interfere with each other

**Login Rate Limit:**
- [ ] 5 failed logins → 6th blocked
- [ ] Successful login on 5th attempt → counter resets
- [ ] VPN/different IP → independent limit

**Edge Cases:**
- [ ] Redis connection failure → allow request (fail open)
- [ ] Concurrent requests → accurate counting
- [ ] Tier-based limits (enterprise = higher limit)

### Notes

<!-- Rate limit configuration, Redis performance, abuse scenarios -->

---

## Phase 3 Summary

**Days Completed:** 0 / 3
**Overall Status:** Not Started

**Key Features Delivered:**
- [ ] User tiers and quotas
- [ ] Admin panel for user management
- [ ] Rate limiting to prevent abuse

**Blockers:** None

**Next Phase:** Phase 4 - Polish & Deploy (Days 11-14)

---

## Sign-Off

**Phase 3 Complete?** [ ] Yes / [ ] No

**Ready for Phase 4?** [ ] Yes / [ ] No

**Notes:**
<!-- Overall phase notes, tier strategy, admin feedback -->
