# PocketBase Setup on Fly.io

This guide walks through creating the homelab-notebook collections on the shared Fly.io PocketBase instance.

**Admin URL:** https://proposaltracker-api.fly.dev/_/

---

## Shared User Account

The `users` collection is shared across all apps using this PocketBase instance. Your existing proposaltracker user account will work for homelab-notebook too.

---

## Collections to Create

You need to create 3 collections: `tags`, `projects`, and `entries` (in that order, due to relations).

---

## Step 1: Create `tags` Collection

1. Go to **Collections** → **New collection**
2. Set **Name**: `tags`
3. Set **Type**: Base collection
4. Add these fields:

| Field Name | Type | Options |
|------------|------|---------|
| `name` | Text | Required, Unique |
| `category` | Select | Values: `technology`, `concept`, `infrastructure`, `other` |
| `color` | Text | (optional, for hex color like `#3b82f6`) |
| `auto_generated` | Bool | Default: false |

5. **API Rules** (set all to require auth):
   - List/Search: `@request.auth.id != ""`
   - View: `@request.auth.id != ""`
   - Create: `@request.auth.id != ""`
   - Update: `@request.auth.id != ""`
   - Delete: `@request.auth.id != ""`

6. Click **Create**

---

## Step 2: Create `projects` Collection

1. Go to **Collections** → **New collection**
2. Set **Name**: `projects`
3. Set **Type**: Base collection
4. Add these fields:

| Field Name | Type | Options |
|------------|------|---------|
| `name` | Text | Required |
| `description` | Text | (plain text, optional) |
| `status` | Select | Values: `active`, `paused`, `completed`, `archived`. Default: `active` |
| `color` | Text | (optional, for hex color) |

5. **API Rules** (same as tags):
   - List/Search: `@request.auth.id != ""`
   - View: `@request.auth.id != ""`
   - Create: `@request.auth.id != ""`
   - Update: `@request.auth.id != ""`
   - Delete: `@request.auth.id != ""`

6. Click **Create**

---

## Step 3: Create `entries` Collection

1. Go to **Collections** → **New collection**
2. Set **Name**: `entries`
3. Set **Type**: Base collection
4. Add these fields:

| Field Name | Type | Options |
|------------|------|---------|
| `mode` | Select | Required. Values: `research`, `project`, `reference` |
| `title` | Text | Required |
| `content` | Editor | (rich text/markdown content) |
| `url` | URL | (optional, for research links) |
| `language` | Text | (optional, for code syntax highlighting) |
| `project` | Relation | Collection: `projects`, Single |
| `tags` | Relation | Collection: `tags`, Multiple |
| `linked_entries` | Relation | Collection: `entries`, Multiple |
| `promoted_from` | Relation | Collection: `entries`, Single |
| `archived` | Bool | Default: false |
| `attachments` | File | Multiple, Max files: 10 |

5. **API Rules** (same as others):
   - List/Search: `@request.auth.id != ""`
   - View: `@request.auth.id != ""`
   - Create: `@request.auth.id != ""`
   - Update: `@request.auth.id != ""`
   - Delete: `@request.auth.id != ""`

6. Click **Create**

---

## Verification

After creating all collections, test the API:

```bash
# Should return empty array (not 404)
curl -s "https://proposaltracker-api.fly.dev/api/collections/entries/records" | jq '.items'

# Should return empty array
curl -s "https://proposaltracker-api.fly.dev/api/collections/projects/records" | jq '.items'

# Should return empty array
curl -s "https://proposaltracker-api.fly.dev/api/collections/tags/records" | jq '.items'
```

---

## Test Authentication

Visit https://homelab-notebook.fly.dev (or https://homelab-notebook.haugaard.app)

1. You should be redirected to `/login`
2. Log in with your existing proposaltracker credentials
3. You should see the empty dashboard

---

## Notes

- The PocketBase instance is shared with proposaltracker
- User accounts work across all apps
- Each app has its own collections (entries, projects, tags are specific to homelab-notebook)
- File storage will use Fly.io's ephemeral storage until Tigris is configured
