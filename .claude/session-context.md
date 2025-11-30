# Session Context - Homelab Notebook

## Last Updated
2024-11-30

## Current Status
**App stuck on loading spinner** - PocketBase is configured and API works, but SvelteKit app won't load.

## What Was Done This Session

### Git Alignment (Completed)
- Merged `alt-brief` branch into `main` (was already done via PR #1 on GitHub)
- Deleted `alt-brief` branch locally and remotely
- `main` is now the single source of truth

### PocketBase Setup (Completed)
- Created dedicated PocketBase instance for this project on **port 8095** (to avoid conflict with act_hub on 8090)
- Fixed docker-compose volume mounts: `/pb_data` and `/pb_migrations` (not `/pb/pb_data`)
- Created superuser via one-time install link (PocketBase 0.34 new flow)
- Created 3 collections: `entries`, `projects`, `tags`
- Set API rules to empty string (allow all) - **NULL = locked, '' = open**

### Configuration Changes
- `docker-compose.yml`: Port changed to 8095:8090, volume paths fixed
- `.env.local`: `PUBLIC_POCKETBASE_URL=http://localhost:8095`

## Current Issue
App shows "Loading your notebook..." spinner indefinitely despite:
- PocketBase API responding correctly: `curl http://localhost:8095/api/collections/entries/records` returns `{"items":[],...}`
- Dev server running on localhost:5173
- Browser console showing no errors

## Next Steps to Debug
1. Check browser Network tab for failed requests
2. Add console.log to `+layout.svelte` onMount to trace where it hangs
3. Verify the stores (`entries.load()`, `projects.load()`, `tags.load()`) are completing
4. Check if there's a promise that never resolves

## Key Files
- `/src/routes/+layout.svelte` - Contains loading state and data loading logic
- `/src/lib/stores/entries.ts` - Entry store with load() method
- `/src/lib/services/pocketbase.ts` - PocketBase client and API calls
- `/.env.local` - PocketBase URL config

## Commands
```bash
# Start PocketBase
docker compose up -d pocketbase

# Start dev server
pnpm dev

# Test API
curl http://localhost:8095/api/collections/entries/records

# PocketBase admin
open http://localhost:8095/_/
```

## PocketBase Credentials
- **Admin URL**: http://localhost:8095/_/
- Created via one-time install link (password set by user)
