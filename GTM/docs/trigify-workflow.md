# Trigify Signal Monitoring — Workflow & Setup

## Connection

- **MCP:** Added via `claude mcp add --transport http --scope user trigify https://api.trigify.io/mcp`
- **Auth:** OAuth via Clerk — browser prompt on first session startup
- **Config location:** `~/.claude.json` → `mcpServers.trigify`
- **No API key needed** — OAuth session token managed by Claude Code automatically

---

## Active Monitors (Solis Campaign)

### Profile Monitors (weekly — named leads)

| Monitor Name | LinkedIn URL | Type |
|---|---|---|
| Karl Khan — Rebuild Sports Clinic | linkedin.com/in/karl-khan-260335a2 | linkedin-profile |
| Barbara Weber — Bodyvie | linkedin.com/in/barbara-weber-37013a4b | linkedin-profile |
| Dr Kowsar Hoque — Aesthetics HQ London | linkedin.com/in/dr-kowsar-hoque-4469a030 | linkedin-profile |
| Fiona Ross — The Aesthetics Club | linkedin.com/in/fiona-ross-a4a986aa | linkedin-profile |
| Pippa O. — Nova Clinic | linkedin.com/in/pippa-o-75ba4b11a | linkedin-profile |

### Keyword Monitors (weekly — LinkedIn Posts)

| Monitor Name | Keywords | keywords_and | Job Titles |
|---|---|---|---|
| RLT + Clinic — Decision Makers | red light therapy, photobiomodulation, RLT | clinic | Owner, Founder, Director, Managing Director, Clinic Director |
| New Treatment + Aesthetic — Decision Makers | new treatment, treatment room, new equipment | aesthetic | Owner, Founder, Director, Managing Director, Clinic Director |
| New Clinic Opening + Wellness — Decision Makers | opening soon, new clinic, new location, second location | wellness | Owner, Founder, Director, Managing Director, Co-Founder |
| Aesthetic Clinic + London — Decision Makers | aesthetic clinic, skin clinic, treatment room | London | Owner, Founder, Director, Managing Director, Clinic Director |
| Wellness Spa + Dubai — Decision Makers | wellness, spa, aesthetic | Dubai | Owner, Founder, Director, Managing Director, Spa Director |
| Recovery Clinic + New Treatment — UK Physio Owners | recovery, physiotherapy, sports clinic | new treatment | Owner, Founder, Director, Managing Director, Clinical Director |
| Biohacking Longevity + Clinic — Decision Makers | biohacking, longevity, wellness technology | clinic | Owner, Founder, Director, Managing Director, Clinic Director |

---

## Key Constraints

- **Max 6 total keywords** across `keywords` + `keywords_and` + `keywords_not` combined for LinkedIn Posts
- **No native geo filter** — use `keywords_and: ["London"]` or `keywords_and: ["Dubai"]` to geo-target
- **Job title filter** supports max 6 titles
- `time_frame` options: `past-month`, `past-year`
- `frequency` options: `WEEKLY`, `DAILY`

---

## Results Processing — ICP Filter Logic

When pulling results, apply this filter to identify genuine ICP matches:

```python
UK_SIGNALS = ['uk', 'united kingdom', 'england', 'london', 'birmingham', 'manchester', 'edinburgh', 'bristol', 'sheffield', 'leeds']
UAE_SIGNALS = ['dubai', 'uae', 'abu dhabi', 'united arab emirates']

ICP_ROLES = ['owner', 'founder', 'director', 'managing director', 'clinic director',
             'spa director', 'head of', 'co-founder', 'principal', 'partner', 'gp', 'dr']

ICP_INDUSTRIES = ['aesthetic', 'clinic', 'spa', 'physio', 'sports', 'wellness',
                  'recovery', 'medical', 'beauty', 'health', 'therapy']

EXCLUDE = ['hiring', 'job', 'vacancy', 'recruiter', 'manufacturer', 'distributor',
           'supplier', 'wholesale', 'university', 'research', 'phd', 'professor']
```

Scoring per result:
- UK geo match: +3
- UAE geo match: +3
- ICP role match: +2
- ICP industry match: +2
- Contains clinic/spa/treatment: +1
- Exclude keyword hit: -5

Only surface results scoring ≥ 4.

---

## Social Listening → Lead Workflow

1. Pull results from Trigify (`get_search_results`)
2. Parse JSON — extract `author.name`, `author.headline`, `content.text`, `author.profile_url`
3. Apply ICP filter above
4. For matches: run Prospeo lookup on LinkedIn URL
5. Add to **Social Listening Outreach** table in Airtable with:
   - Primary Signal: quote the exact LinkedIn post text that triggered
   - ICP Score: apply standard scoring (A/B/C/DQ)
   - Domain: from Prospeo or manual lookup
6. Do NOT merge into main leads table until lead responds or books a call

---

## Results Found (Solis Campaign — Feb 2026)

| Name | Company | Location | Signal | Score | Table |
|---|---|---|---|---|---|
| Karla Perentin | Nature Heals | London ✅ | "LLLT in our London clinics" | 75 (B) | Social Listening |
| Jonathan Collins | Spirit of Man | Sheffield ✅ | "private treatment rooms... guys in Sheffield" | 80 (A) | Social Listening |
| Sarah Farrant | BCLA | Taunton ✅ | Presenting on PBM at 100% Optical UK | 72 (B) | Social Listening |

---

## Troubleshooting

**Results too noisy / global:** Add `keywords_and` with a location name. Switch from `past-year` to `past-month` if needed.

**No results returned:** Monitor is still processing — wait 15-20 seconds and retry `get_search_results`.

**OAuth breaks between sessions:** Just restart Claude Code — it re-authenticates automatically on session start.

**Result exceeds token limit:** Saved to file at `~/.claude/projects/.../tool-results/`. Use Bash + Python to parse the file directly.
