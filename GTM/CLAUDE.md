# Falcon GTM — Project Intelligence

This is the Falcon GTM system: an AI-native B2B outbound engine for clients. The first live campaign is **Solis Therapy** (UK + Dubai). This file is the foundation for all skills, agents, and workflows.

## What Falcon GTM Is

A repeatable, AI-powered outbound system that:
1. Builds ICP-matched lead lists from multiple sources
2. Enriches leads with contact data (Prospeo), social signals (Trigify), and firmographic data
3. Scores and prioritises leads into tiered outreach queues
4. Syncs all data bidirectionally across Supabase + Airtable
5. Generates personalised cold email sequences per persona
6. Monitors live buying signals weekly via Trigify

**The goal:** Deliver a warm, signal-enriched pipeline to the client ready for outreach — not a raw CSV.

---

## Active Client: Solis Therapy

- **Website:** solistherapy.com
- **Product:** Clinical-grade red light therapy (RLT) panels
- **Campaign type:** B2B outbound — net-new channel (currently DTC only)
- **Target geos:** UK + Dubai
- **GTM Playbook:** `gtm-playbook-solis-therapy-2026-02-25.md`
- **Targeting Ideas:** `targeting-ideas-solis-therapy-2026-02-25.html`

See `docs/solis-campaign.md` for full campaign context.

---

## Tech Stack

| Tool | Purpose | Key Docs |
|------|---------|----------|
| **Supabase** | Primary DB — companies + leads | Project: `zobnvslfgallvljulcjh` |
| **Airtable** | Client-facing CRM view | Base: `appJxqsjEGRNn2iQ6` |
| **Trigify MCP** | Live LinkedIn/Twitter signal monitoring | See `docs/trigify-workflow.md` |
| **Prospeo API** | Email + mobile enrichment | Key: `pk_7aa165d8b92e7e18b4c946f503c5ebb26c3f289cc81253cfe068cec637f530d9` |
| **Apollo API** | People/org search + enrichment | Key: `APOLLO_API_KEY` in `.env` |
| **Clay** | List building + outreach sequencing | — |

---

## Database Schema

### Supabase: `solis_leads`
Core fields: `full_name`, `email`, `phone`, `title`, `company_id`, `industry`, `location`, `icp_score`, `icp_tier`, `primary_signal`, `domain`, `linkedin_url`, `email_status`, `notes`

### Supabase: `solis_companies`
Core fields: `company_name`, `website`, `industry`, `vertical`, `domain`, `location`, `notes`

### Airtable Tables
- **Main Leads** (`tbl...`) — all enriched leads, mirrors Supabase
- **Social Listening Outreach** (`tblFg31poB5dlsMGU`) — leads sourced from Trigify monitors
- Both tables have: Name, Email, Company, Title, Location, ICP Score, ICP Tier, Domain, Primary Signal, Industry, Notes, Documents

---

## ICP Scoring Logic

| Score | Tier | Label | Criteria |
|-------|------|-------|----------|
| 80–100 | A | Hot | UK/Dubai, buyer title, RLT-adjacent signals |
| 65–79 | B | Warm | UK/Dubai, relevant industry, no direct signal |
| 50–64 | C | Cold | Wrong geo or weak buying signal |
| <50 | DQ | Disqualified | Outside market or not a buyer |

**Key verticals:** `aesthetics`, `sports_physio`, `gym_recovery`, `medical`

---

## Trigify Monitor Setup

See `docs/trigify-workflow.md` for full monitor list and ICP filter logic.

**Active monitors:**
- 5 x LinkedIn Profile monitors (named key leads — weekly)
- 3 x LinkedIn Post keyword monitors (RLT + clinic, aesthetic + treatment, biohacking + clinic)
- Geo-targeted: London + aesthetic, Dubai + wellness, UK physio + new treatment

**Key constraint:** Trigify LinkedIn search has no native geo filter — use `keywords_and` with location names (e.g. `["London"]`) to geo-filter. Max 6 total keywords across OR/AND/NOT.

---

## Prospeo Enrichment

Used to find verified emails and phone numbers for leads.

```js
// Standard lookup pattern
POST https://api.prospeo.io/linkedin-email-finder
{ "url": "https://linkedin.com/in/..." }

POST https://api.prospeo.io/social-url-search
{ "url": "...", "required_field": "PROFESSIONAL_EMAIL" }
```

Response scoring: `VERIFIED` > `VALID` > `ACCEPT_ALL` > skip.

---

## Workflow Sequence (Replicable Per Client)

1. **GTM Playbook** — run `/gtm-playbook` skill → produces ICP, personas, verticals
2. **Targeting** — run `/targeting-ideas` skill → produces 10+ ICP segments
3. **Lead sourcing** — two methods:
   - **Apollo API** — programmatic search via `POST /api/v1/mixed_people/api_search` (see Apollo Integration below)
   - **Clay import** — export CSV from Clay and seed into Supabase/Airtable
4. **DB setup** — create Supabase tables + Airtable base from `solis-leads/` scripts
5. **Enrichment** — run Prospeo on all leads → populate emails, phones, titles
6. **Signal monitoring** — set up Trigify monitors (profile + keyword)
7. **Scoring** — apply ICP scoring logic → populate icp_score + icp_tier
8. **Social listening** — pull Trigify results weekly → filter for geo + ICP match → add to Social Listening table
9. **Dossiers** — for leads that reply or book: run dossier workflow (see below)
10. **Sequences** — run `/write-sequence` skill per persona → output cold email copy
11. **Launch** — load into Clay / outreach tool

---

## Dossier Workflow

One-page prospect dossier built per lead, exported as PDF, and attached to Airtable.

### When to Run
- Lead replies to cold outreach (any reply — positive or objection)
- Lead books a call or meeting
- Lead is escalated to Tier A during scoring review
- Social listening surfaces a high-signal UK/Dubai prospect

### What a Dossier Contains
1. Header — name, company, location, title, ICP score + tier, recommended product
2. Clinic overview — what they do, who they serve, positioning
3. Current technology stack — what modalities they already use, with RLT gap flagged
4. Key stats — locations, staff size, review score, social following
5. Press & recent news — any coverage, awards, expansions in last 12 months
6. 3 personalisation hooks — specific, research-backed angles for the call
7. 3 likely objections + counters — anticipate what they'll push back on
8. Contact strip — email, website, Instagram, LinkedIn, current status

### Research Process
Run 5 parallel research agents (one per lead) using the `general-purpose` subagent type. Each agent should fetch:
- Company website (homepage, services, about, pricing, team pages)
- Trustpilot / Google reviews
- LinkedIn profile (public activity, reposts, follower count)
- Instagram (handle, follower count, posting themes)
- Any press coverage (search "[name] interview", "[company] award", "[company] featured in")
- Any RLT / LED / photobiomodulation mentions on their site or socials

Return as structured JSON with these fields:
```
website_summary, treatments_offered, existing_modalities, rlt_mentions,
client_base, pricing_tier, locations, staff_size, social_activity,
press_coverage, recent_news, instagram_handle, key_hooks, objection_risks
```

### Build Process
1. **Research** — launch parallel agents for all leads in batch
2. **HTML** — build dossier as styled HTML using the dark-header / gold-accent design (see `dossiers/*.html` for reference template)
3. **PDF** — convert using headless Chrome:
   ```bash
   "/Applications/Google Chrome.app/Contents/MacOS/Google Chrome" \
     --headless --disable-gpu --no-sandbox \
     --print-to-pdf="output.pdf" \
     --print-to-pdf-no-header \
     "file:///path/to/dossier.html"
   ```
4. **Host** — upload PDF to tmpfiles.org (temporary URL — Airtable fetches & stores permanently):
   ```bash
   # Upload returns page URL
   PAGE_URL=$(curl -s -F "file=@file.pdf" https://tmpfiles.org/api/v1/upload | python3 -c "import sys,json; print(json.load(sys.stdin)['data']['url'])")
   # Convert to direct download link (insert /dl/ after domain)
   DL_URL=$(echo "$PAGE_URL" | sed 's|tmpfiles.org/|tmpfiles.org/dl/|')
   ```
   **Why tmpfiles.org:** Airtable cannot fetch from catbox.moe (silently returns 0 bytes). tmpfiles.org links expire after ~1hr but Airtable downloads and permanently hosts its own copy.
5. **Attach to Airtable** — PATCH the record's `Dossier` field with the tmpfiles DL URL:
   ```js
   PATCH https://api.airtable.com/v0/{baseId}/{tableId}/{recordId}
   { "fields": { "Dossier": [{ "url": "http://tmpfiles.org/dl/xxxxx/name.pdf", "filename": "name.pdf" }] } }
   ```

### Airtable Field
- Field name: `Dossier`
- Field type: `multipleAttachments`
- Present in: Outreach table (`tblcHXaxaxqQ4KFpn`) and Social Listening Outreach table

### Files
- HTML source: `GTM/dossiers/{slug}.html`
- PDF export: `GTM/dossiers/{slug}.pdf`
- Naming convention: `{firstname}-{lastname}-{company-slug}.html` (all lowercase, hyphens)

### Design Reference
See any file in `GTM/dossiers/` for the HTML template. Key design decisions:
- Dark header (`#0f1923`) with gold accents (`#c9a96e`)
- Score circle (gold, top-right of header)
- Tags for modalities — green (`warm`) for existing RLT presence, gold (`highlight`) for gaps
- Black callout block for the single most important intel (e.g. "already uses RLT in 3 forms")
- Hook cards (gold left-border) and objection cards (grey left-border)
- Print-friendly — `@media print` override removes background

---

## Key Files

```
GTM/
├── CLAUDE.md                                    ← This file
├── docs/
│   ├── solis-campaign.md                        ← Full Solis campaign context
│   └── trigify-workflow.md                      ← Trigify monitor setup + results
├── dossiers/
│   ├── karl-khan-rebuild-sports-clinic.html     ← HTML source (design reference)
│   ├── karl-khan-rebuild-sports-clinic.pdf      ← Exported PDF
│   ├── barbara-weber-bodyvie.html
│   ├── barbara-weber-bodyvie.pdf
│   ├── fiona-ross-aesthetics-club.html
│   ├── fiona-ross-aesthetics-club.pdf
│   ├── dr-kowsar-hoque-aesthetics-hq.html
│   ├── dr-kowsar-hoque-aesthetics-hq.pdf
│   ├── pippa-o-nova-clinic.html
│   └── pippa-o-nova-clinic.pdf
├── solis-leads/
│   ├── airtable-build.js                        ← Create Airtable tables + fields
│   ├── airtable-seed.js                         ← Seed Airtable from CSV
│   ├── create-tables.js                         ← Create Supabase tables
│   └── seed.js                                  ← Seed Supabase
├── gtm-playbook-solis-therapy-2026-02-25.md
└── targeting-ideas-solis-therapy-2026-02-25.html
```

---

## Conventions

- Always keep Supabase and Airtable in sync — update both when adding/changing lead data
- Domains go in a `Domain` field (URL type in Airtable)
- Industry uses title case: `"Physiotherapy & Sports Medicine"`, `"Aesthetics & Beauty"`, `"Health & Wellness"`, `"Regenerative Medicine"`
- Verticals in DB use snake_case: `sports_physio`, `gym_recovery`, `medical`, `aesthetics`
- Never commit API keys — they live in scripts only
- Social Listening leads stay in their own table — don't merge with main leads until qualified

---

## Instantly Integration

### Auth & Config
- **API Key:** `INSTANTLY_API_KEY` in `GTM/solis-leads/.env` (base64 encoded)
- **MCP:** Configured in Claude Desktop (`claude_desktop_config.json`) — remote server at `https://mcp.instantly.ai/mcp`
- **Base URL:** `https://api.instantly.ai/api/v2/`
- **Auth header:** `Authorization: Bearer $INSTANTLY_API_KEY`
- **Workspace org:** `703af03c-c35d-4c82-a420-e76c2672a31a`

### Active Campaign
- **Name:** Solis B2B - Aesthetic Clinics and Medspas (UK + Dubai)
- **ID:** `bd115e36-c01e-4089-b14e-5066515c5f9c`
- **Status:** Draft (0) — not yet activated
- **Schedule:** Mon-Fri 09:00-17:00, timezone `Europe/Isle_of_Man` (GMT+0)
- **Sequence:** 4 steps (Day 0, +3d, +3d, +5d) — see Instantly dashboard for copy
- **Leads loaded:** 5 (Fiona Ross, Dr Kowsar Hoque, Karl Khan, Pippa O., Barbara Weber)

### API Gotchas (Learned the Hard Way)
1. **Timezone enum is broken:** `Europe/London`, `Europe/Berlin`, `Europe/Paris` all REJECTED. Use `Europe/Isle_of_Man` for UK GMT+0 or `Africa/Casablanca`
2. **Single vs bulk lead add:** Single lead = flat body with `"campaign"` field. Bulk = `"leads"` array with `"campaign_id"` field. Different field names for the same concept
3. **Shell escaping:** Complex JSON with quotes/apostrophes breaks inline curl. Always write to `/tmp/payload.json` and use `-d @/tmp/payload.json`
4. **Python urllib blocked:** Cloudflare error 1010 — always use `curl`, never Python's urllib/requests for Instantly API
5. **`source .env` in subshells:** Doesn't export. Use `export $(grep INSTANTLY_API_KEY GTM/solis-leads/.env)` instead
6. **`campaign_schedule` is required** on campaign creation — no default
7. **Reply typo:** The reply field is `reyply_to_uuid` (Instantly's actual typo, not ours)
8. **Interest status is numeric:** 1 = Interested, -1 = Not Interested, 2 = Meeting Booked, etc.
9. **List leads is POST not GET:** `POST /api/v2/leads/list` (complex filter body)

### Airtable Field Mapping
| Airtable Field | Instantly Field |
|----------------|----------------|
| Full Name | `first_name` + `last_name` (split) |
| Email | `email` |
| Company | `company_name` |
| Job Title | `custom_variables.title` |
| Location | `custom_variables.location` |
| Push to Campaign | Internal flag (set to "Yes" when pushed) |

### Skill Reference
Full API reference: `~/.claude/skills/instantly-api/SKILL.md`

---

## Apollo Integration

### Auth & Config
- **API Key:** `APOLLO_API_KEY` in `GTM/solis-leads/.env`
- **Base URL:** `https://api.apollo.io`
- **Auth header:** `x-api-key: $APOLLO_API_KEY` (also accepts `api_key` in request body)
- **Always include:** `Cache-Control: no-cache` and `Content-Type: application/json`

### When to Use
- **Programmatic lead sourcing** — search Apollo's 270M+ person database by title, location, company size, industry keywords, tech stack, funding
- **People enrichment** — get verified emails, phone numbers, employment history for leads found via Clay or other sources
- **Org enrichment** — get company details (revenue, funding, tech stack, headcount) by domain

### Key Endpoints

| Action | Method | Endpoint | Credits |
|--------|--------|----------|---------|
| Search people (net new) | POST | `/api/v1/mixed_people/api_search` | No |
| Search orgs (net new) | POST | `/api/v1/mixed_companies/search` | No |
| Enrich person | POST | `/api/v1/people/match` | Yes |
| Enrich org | GET | `/api/v1/organizations/enrich?domain=X` | No |
| Bulk enrich people (10/req) | POST | `/api/v1/people/bulk_match` | Yes |

### Typical Solis Search Pattern

```bash
export $(grep APOLLO_API_KEY GTM/solis-leads/.env)
curl -X POST "https://api.apollo.io/api/v1/mixed_people/api_search" \
  -H "x-api-key: $APOLLO_API_KEY" \
  -H "Content-Type: application/json" \
  -H "Cache-Control: no-cache" \
  -d @/tmp/apollo-search.json
```

```json
{
  "person_titles": ["clinic owner", "clinic director", "medical director"],
  "person_seniorities": ["owner", "founder", "c_suite", "director"],
  "organization_locations": ["London", "Dubai"],
  "organization_num_employees_ranges": ["5,50"],
  "q_organization_keyword_tags": ["aesthetics", "wellness", "physiotherapy"],
  "contact_email_status": ["verified"],
  "per_page": 25,
  "page": 1
}
```

### API Gotchas
1. **People Search ≠ Contact Search:** `/mixed_people/api_search` = Apollo's database (net new). `/contacts/search` = your CRM only
2. **Employee ranges are strings:** `"10,50"` not `[10, 50]`
3. **No industry filter:** Use `q_organization_keyword_tags[]` for industry keywords
4. **Revenue is raw integers:** `revenue_range[min]: 500000` not `$500K`
5. **Search returns limited data:** Name (last name obfuscated), title, has_email. Must enrich to get actual email/phone
6. **Rate limits:** 200/min, 600/hr, 6000/day. Bulk endpoints: 20/min
7. **Phone reveal is async:** Set `reveal_phone_number: true` + provide `webhook_url` — phones delivered via webhook

### Skill Reference
Full API reference: `~/.claude/skills/apollo-api/SKILL.md` and `~/.claude/skills/apollo-api/apollo-api-reference.md`
