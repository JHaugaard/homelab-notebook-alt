# Homelab Notebook

> A three-mode knowledge management system for capturing, organizing, and retrieving technical knowledge during active learning and project work.

**Status**: Active Development | **Developer**: John | **Philosophy**: Delivery-Focused, Best Practices

---

## Developer Profile

- **Experience**: Hobbyist developer, beginner-to-intermediate
- **Learning Goal**: Deep understanding of full-stack, professional practices
- **Reliance**: Heavy use of Claude Code for implementation
- **Common Tasks**: Feature implementation, debugging, refactoring, testing, deployment

---

## Project Overview

### What It Does
Homelab Notebook is a unified knowledge repository organized around three integrated modes:

- **Research Mode**: Capture and organize external resources (links, articles, documentation) encountered during learning
- **Project Mode**: Developer journal with timestamped log entries during active project work
- **Reference Mode**: Distilled, tutorial-style documentation that anyone can follow

### Core Concepts
- **Mode-first navigation**: Entries belong to a primary mode (Research/Project/Reference)
- **Mode promotion**: Entries can be promoted (Research → Project → Reference) or cross-graded
- **Projects as optional organizers**: Entries can exist independently or be associated with a project
- **Timeline-based Project mode**: Discrete, timestamped entries like a developer journal
- **AI-ready architecture**: Clear extension points for future auto-tagging and content enhancement

### Tech Stack

| Layer | Technology | Purpose |
|-------|------------|---------|
| Frontend | **SvelteKit** | Fast hydration, built-in routing, excellent DX |
| Backend | **PocketBase** | Auth, database (SQLite), API in single binary |
| Styling | **Tailwind CSS v4** | Utility-first CSS for rapid iteration |
| Search | **FlexSearch.js** | Client-side instant fuzzy search |
| Code Editor | **CodeMirror 6** | Syntax highlighting for snippets |
| Icons | **Lucide Svelte** | Lightweight, consistent icon set |

### Architecture Decisions
- **Client-side search**: All entry metadata loaded on startup, indexed with FlexSearch for 0ms search latency
- **Single-user**: No complex auth; simple PocketBase auth for access control
- **Docker-first**: Both dev and prod run in containers for consistency
- **Mode-aware UI**: Each mode has tailored views (cards for Research, timeline for Project, documents for Reference)

---

## Development Environment

### Computers & Sync
- MacBook Pro and Mac Mini
- Sync via iCloud, portable SSDs, GitHub

### Prerequisites
- Node.js 20+ (via nvm recommended)
- Docker & Docker Compose
- pnpm (preferred package manager)

### First-Time Setup

```bash
# Clone and install
cd homelab-notebook-alt
pnpm install

# Copy environment file
cp .env.example .env.local

# Start PocketBase (local dev instance)
docker compose up -d pocketbase

# Run development server
pnpm dev
```

Open http://localhost:5173 to see the app.

### IDE Setup
- VS Code with extensions: Svelte for VS Code, Tailwind CSS IntelliSense, ESLint, Prettier

---

## Infrastructure & Hosting

### Fly.io (Production)

This project is deployed on Fly.io, sharing a PocketBase instance with other apps.

| Component | URL | Notes |
|-----------|-----|-------|
| **App** | https://homelab-notebook.fly.dev | Primary Fly.io URL |
| **App** | https://homelab-notebook.haugaard.app | Custom domain |
| **PocketBase API** | http://proposaltracker-api.internal:8080 | Internal Fly network |
| **PocketBase Admin** | https://proposaltracker-api.fly.dev/_/ | Admin UI |

### Shared PocketBase Instance

The PocketBase instance (`proposaltracker-api`) is shared across multiple apps:
- User accounts work across all apps (single sign-on)
- Each app has its own collections (`entries`, `projects`, `tags` for this app)
- Internal networking via Fly's private network

### VPS Infrastructure (Other Services)

The Hostinger VPS hosts other homelab services (not this project):
- **VPS**: 8 cores, 32GB RAM, 400GB storage
- **Domain**: haugaard.dev
- **Services**: Supabase, n8n, Ollama, etc.

---

## Development Workflow

### Git Branching
- `main`: Production-ready code
- `dev`: Active development branch
- Feature branches: `feature/descriptive-name`

### Commit Convention
```
type(scope): description

feat(search): add fuzzy matching with FlexSearch
fix(capture): resolve paste formatting issues
chore(deps): update SvelteKit to latest
```

Types: `feat`, `fix`, `docs`, `style`, `refactor`, `test`, `chore`

### Development Cycle
1. Create feature branch from `dev`
2. Implement with tests
3. PR to `dev`, squash merge
4. Periodic releases: `dev` → `main`

### Testing Strategy
- **Unit**: Vitest for utilities and stores
- **Component**: Svelte Testing Library
- **E2E**: Playwright (when needed)

---

## Code Conventions

### File Organization
```
src/
├── lib/
│   ├── components/
│   │   ├── layout/       # AppShell, Sidebar, Header
│   │   ├── nav/          # ModeNav, ProjectList, GlobalSearch
│   │   ├── entries/      # EntryCard, EntryForm, TimelineEntry
│   │   ├── editor/       # MarkdownEditor, CodeEditor
│   │   ├── ui/           # Button, Input, Modal, Badge, TagInput
│   │   └── features/     # ProjectCard, PromoteModal, QuickCaptureModal
│   ├── stores/           # Svelte stores (entries, projects, tags, search, ui)
│   ├── services/         # PocketBase client, AI hooks (future)
│   ├── utils/            # Helper functions
│   └── types/            # TypeScript types
├── routes/
│   ├── +layout.svelte    # App shell with sidebar
│   ├── +page.svelte      # Dashboard
│   ├── research/         # Research mode routes
│   ├── project/          # Project mode routes
│   ├── reference/        # Reference mode routes
│   ├── projects/         # Project management routes
│   ├── tags/             # Tag management
│   └── settings/         # User preferences
tests/
static/
```

### Naming Conventions
- Components: PascalCase (`EntryCard.svelte`)
- Files: kebab-case (`entry-utils.ts`)
- Functions: camelCase (`formatDate()`)
- Constants: SCREAMING_SNAKE_CASE (`MAX_TITLE_LENGTH`)
- Types: PascalCase (`Entry`, `Project`, `Tag`)

### Code Style
- ESLint + Prettier for formatting
- Explicit types (avoid `any`)
- Prefer composition over inheritance
- Small, focused functions

---

## Common Commands

### Development
```bash
pnpm dev              # Start dev server (http://localhost:5173)
pnpm build            # Build for production
pnpm preview          # Preview production build
pnpm check            # Run svelte-check
pnpm lint             # Run ESLint
pnpm format           # Run Prettier
```

### Testing
```bash
pnpm test             # Run Vitest
pnpm test:ui          # Vitest with UI
pnpm test:coverage    # Generate coverage report
```

### Docker
```bash
docker compose up -d              # Start all services
docker compose up -d pocketbase   # Start only PocketBase
docker compose logs -f            # Follow logs
docker compose down               # Stop services
```

### Database (PocketBase)
```bash
# Access PocketBase admin UI
open http://localhost:8090/_/

# Backup database (from container)
docker compose exec pocketbase cp /pb/pb_data/data.db /pb/pb_data/backup.db
```

---

## Data Model

### PocketBase Collections

**entries** (main content collection)
| Field | Type | Notes |
|-------|------|-------|
| id | string | Auto-generated |
| mode | select | 'research', 'project', 'reference' |
| title | string | Required |
| content | text | Markdown content |
| url | url | For research links |
| language | string | For code snippets (syntax highlighting) |
| project | relation | Optional link to projects collection |
| tags | relation | Many-to-many with tags collection |
| linked_entries | relation | Cross-references to other entries |
| promoted_from | relation | Tracks mode transitions |
| archived | bool | Soft delete |
| created | datetime | Auto |
| updated | datetime | Auto |

**projects**
| Field | Type | Notes |
|-------|------|-------|
| id | string | Auto-generated |
| name | string | Required |
| description | text | Optional |
| status | select | 'active', 'paused', 'completed', 'archived' |
| color | string | Hex color for UI |
| created | datetime | Auto |
| updated | datetime | Auto |

**tags**
| Field | Type | Notes |
|-------|------|-------|
| id | string | Auto-generated |
| name | string | Unique, lowercase, normalized |
| category | select | 'technology', 'concept', 'infrastructure', 'other' |
| color | string | Hex color for UI |
| auto_generated | bool | For future AI tagging |

### Environment Variables

```bash
# .env.local (development)
PUBLIC_POCKETBASE_URL=http://localhost:8090
```

Production environment variable is set via Fly.io secrets:

```bash
# View current secrets
fly secrets list

# The production PocketBase URL uses Fly's internal network
fly secrets set PUBLIC_POCKETBASE_URL="http://proposaltracker-api.internal:8080"
```

### Authentication Flow
1. User visits app → redirected to login if not authenticated
2. PocketBase handles auth (email/password for single user)
3. Auth token stored in cookie (httpOnly)
4. All API requests include auth token

### Search Implementation
1. On app load, fetch all entries metadata (id, title, tags, mode, project)
2. Build FlexSearch index on client
3. Search queries filter locally (0ms latency)
4. Results grouped by mode in search UI
5. Full entry content fetched on selection

---

## UI/UX Reference

See `.docs/ui-ux-redesign-v2.md` for complete specification including:
- Wireframes for all modes
- Component inventory
- User flows
- Keyboard shortcuts
- Visual design guidelines

### Mode Color Coding
| Mode | Color | Tailwind Class |
|------|-------|----------------|
| Research | Blue | `text-blue-500`, `bg-blue-50` |
| Project | Amber | `text-amber-500`, `bg-amber-50` |
| Reference | Green | `text-green-500`, `bg-green-50` |

### Keyboard Shortcuts
| Shortcut | Action |
|----------|--------|
| `⌘K` | Open global search |
| `⌘N` | New entry (opens mode selector) |
| `⌘1` | Switch to Research mode |
| `⌘2` | Switch to Project mode |
| `⌘3` | Switch to Reference mode |
| `⌘S` | Save current entry |
| `Esc` | Close modal / cancel edit |

---

## Deployment

### Deployment Workflow

Deployment is automated via GitHub Actions:

1. Push to `main` branch
2. CI workflow runs (lint, type-check, build)
3. On CI success, Deploy workflow triggers automatically
4. Fly.io builds and deploys the Docker image

### Manual Deployment

```bash
# Deploy directly (bypasses CI)
fly deploy

# Check status
fly status
fly logs
```

### Deployment Checklist

- [ ] All tests passing locally
- [ ] Build succeeds (`pnpm build`)
- [ ] Environment variables set (`fly secrets list`)
- [ ] PocketBase collections configured

### Rollback Procedure

```bash
# List recent deployments
fly releases

# Roll back to previous version
fly deploy --image <previous-image>
```

---

## Resources & References

### Project Docs
- [SvelteKit Docs](https://svelte.dev/docs/kit)
- [PocketBase Docs](https://pocketbase.io/docs/)
- [Tailwind CSS v4](https://tailwindcss.com/docs)
- [FlexSearch](https://github.com/nextapps-de/flexsearch)
- [CodeMirror 6](https://codemirror.net/)

### Project-Specific Docs
- `.docs/ui-ux-redesign-v2.md` - UI/UX specification
- `.docs/homelab-notebook-brief-v1.md` - Project brief
- `.docs/tech-stack-decision.md` - Technology choices
- `.docs/deployment-strategy.md` - Deployment plan

### Fly.io Infrastructure

- App config: `fly.toml`
- Deployment logs: `fly logs`
- PocketBase admin: <https://proposaltracker-api.fly.dev/_/>

---

## Troubleshooting

### PocketBase Connection Failed

```
Error: Failed to connect to PocketBase
```

**Local development**: Ensure PocketBase container is running: `docker compose up -d pocketbase`

**Production**: Check Fly.io internal networking:

```bash
fly ssh console
curl http://proposaltracker-api.internal:8080/api/health
```

### Tailwind Classes Not Working
**Solution**: Check that the file is included in `tailwind.config.ts` content paths

### Search Not Finding Results
**Solution**: FlexSearch index may be stale. Refresh the page to rebuild index.

### Build Fails with Type Errors
```bash
pnpm check  # See detailed type errors
```

---

## Skill Location

When there is a specific reference to a Claude Skill, or the context indicates that a Claude Skill should be invoked, note that all skills used in this project are personal skills and located at: /Users/john/.claude/skills
