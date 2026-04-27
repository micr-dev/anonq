# Nightshift: Doc Drift Detector — anonq

**Repository:** micr-dev/anonq  
**Date:** 2026-04-04  
**Category:** analysis / doc-drift  
**Severity Scale:** P0 Critical, P1 High, P2 Medium, P3 Low

---

## Summary

anonq is an anonymous Q&A platform built with Next.js 15, Auth0, and Supabase. The README provides setup instructions and environment variable documentation. Analysis found several documentation drift issues where the code has evolved beyond what the README describes.

---

## Findings

### P2: Missing environment variables in README
**File:** `README.md` (L33-45) vs `lib/config.ts` (L27-44)

The README lists these environment variables:
- `AUTH0_SECRET`, `AUTH0_DOMAIN`, `AUTH0_CLIENT_ID`, `AUTH0_CLIENT_SECRET`
- `APP_BASE_URL`, `NEXT_PUBLIC_SITE_URL`
- `NEXT_PUBLIC_SUPABASE_URL`, `NEXT_PUBLIC_SUPABASE_ANON_KEY`
- `ALLOWED_ADMIN_EMAILS`, `OPENAI_API_KEY`, `NTFY_URL`

**Missing from README:**
- `API_PROVIDER` (L27 in config.ts) — switches between `'openai'` and `'custom'` providers
- `CUSTOM_API_URL` (L30) — custom API endpoint URL
- `CUSTOM_API_KEY` (L31) — custom API key
- `CUSTOM_API_MODEL` (L32) — custom model name (defaults to `'gpt-3.5-turbo'`)
- `CUSTOM_API_MAX_TOKENS` (L43) — max tokens for custom provider
- `CUSTOM_API_TEMPERATURE` (L44) — temperature for custom provider
- `OPENAI_MODEL` (L58) — OpenAI model override
- `OPENAI_MAX_TOKENS` (L59) — max tokens override
- `OPENAI_TEMPERATURE` (L60) — temperature override
- `ADMIN_PASSWORD_HASH` (L69) — mentioned in `validateEnvironment()` but absent from README env table
- `NEXT_PUBLIC_NTFY_URL` (L23 in config.ts) — alternative ntfy URL env var
- `PORT` (L65) — server port override

### P2: README mentions `.env.example` but file doesn't exist in repo
**File:** `README.md` (L13)

```bash
cp .env.example .env
```

No `.env.example` file exists in the repository. New developers following setup instructions will hit a dead end.

### P2: README says "React 18" but package.json shows React 18.3.1
**File:** `README.md` (L7) vs `package.json` (L26)

README states "React 18" generically. While technically correct, the package.json pins `^18.3.1`. Not a significant drift, but the README could be more precise.

### P3: README documents `npm run dev` scripts but `test` script is a no-op
**File:** `README.md` (L49-51) vs `package.json` (L9)

The README doesn't mention testing at all, and the test script is:
```json
"test": "echo \"Error: no test specified\" && exit 1"
```

This is fine for a small project but should be acknowledged.

### P3: `lib/config.ts` references `ADMIN_PASSWORD_HASH` but no admin password auth is used
**File:** `lib/config.ts` (L68-74)

The `getAdminPasswordHash()` method exists but Auth0 is the actual auth mechanism (see `middleware.ts` and `lib/auth0.ts`). This suggests the password hash was from an earlier auth system that was replaced by Auth0 but the code wasn't fully cleaned up.

### P3: Middleware matcher excludes `quarzite.png`
**File:** `middleware.ts` (L10)

```typescript
"/((?!_next/static|_next/image|favicon.ico|sitemap.xml|robots.txt|quarzite.png).*)"
```

The `quarzite.png` exclusion appears to be a leftover from the Quarzite skin project — it's unusual for an anonymous Q&A app to reference a Quarzite asset. Not documented anywhere.

---

## Recommended Documentation Updates

1. **Add missing env vars** to the Environment Variables table in README
2. **Create `.env.example`** file with all required variables and placeholder values
3. **Remove or document** the `ADMIN_PASSWORD_HASH` legacy code path
4. **Document** the custom API provider configuration section
5. **Note** the `quarzite.png` middleware exclusion or remove it
