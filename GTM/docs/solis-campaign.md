# Solis Therapy — Campaign Context

## Client Overview

- **Company:** Solis Therapy
- **Website:** solistherapy.com
- **Phone:** 0203 918 3771
- **Product:** Clinical-grade red light therapy (RLT) panels — Spectrum Series
- **Current model:** DTC e-commerce only (B2C)
- **Campaign goal:** Launch net-new B2B wholesale/clinic channel in UK + Dubai

## Product Line

| Product | LEDs | Coverage | Sale Price (GBP) | B2B Use Case |
|---|---|---|---|---|
| SOLO 2.0 | 60 | Targeted | £359 | Entry clinic add-on |
| SURGE 2.0 | 180 | Half body | £749 | Treatment room mid-tier |
| SUPREME 2.0 | 308 | Half body | £899 | Best seller — ideal B2B |
| STUDIO 1.0 | 960 | Full body | £1,979 | Premium clinic/wellness |
| SPARK 2.0 | Handheld | Portable | £229 | Retail resale add-on |
| SPOT Torch | Torch | Pinpoint | £129 | Retail add-on |

Key differentiators: 8,000+ clinical studies, 4.8/5 Trustpilot, 90-day money-back, 3-year warranty, award-winning design.

## ICP Verticals

| Vertical | DB Key | Industry Label |
|---|---|---|
| Aesthetic Clinics | `aesthetics` | Aesthetics & Beauty |
| Sports Physio Clinics | `sports_physio` | Physiotherapy & Sports Medicine |
| Gyms / Recovery Centres | `gym_recovery` | Health & Wellness |
| Regenerative / Medical | `medical` | Regenerative Medicine |

## Target Personas

1. **Aesthetic Clinic Owner** — adding RLT to skin/anti-ageing menu
2. **Sports Physio Clinic Owner** — muscle recovery, injury treatment protocol
3. **Gym/Wellness Owner** — recovery suite differentiation
4. **Medical/Regenerative Director** — evidence-based treatment expansion

## Top 5 Leads (Email Confirmed)

| Name | Company | Email | Vertical |
|---|---|---|---|
| Joshua Bryant | Physio Athletic | — | sports_physio |
| Karl Khan | Rebuild Sports Clinic | — | sports_physio |
| Ron Bell | Sporting Recovery | — | gym_recovery |
| Mais Alomar | The Regenerative Clinic | — | medical |
| Dr Shaan Patel | EC Clinic London | — | aesthetics |

(Emails enriched via Prospeo — stored in Supabase `solis_leads` + Airtable main table)

## Data Infrastructure

### Supabase
- Project ID: `zobnvslfgallvljulcjh`
- Tables: `solis_companies`, `solis_leads`
- Key fields: `icp_score`, `icp_tier`, `primary_signal`, `vertical`, `industry`, `domain`

### Airtable
- Base ID: `appJxqsjEGRNn2iQ6`
- Token: `patPi85DqOiA5BsZM...` (stored in scripts)
- Tables: Main Leads, Social Listening Outreach
- Both tables have: Domain (URL), Documents (attachment), Industry, ICP Score/Tier, Primary Signal

## Campaign Status (as of 2026-02-25)

- [x] GTM Playbook generated
- [x] Targeting ideas generated (10+ ICP segments)
- [x] Lead list built (41 companies, 41+ leads)
- [x] Supabase schema created and seeded
- [x] Airtable base created and seeded
- [x] Prospeo enrichment run — top 5 have verified emails
- [x] Trigify MCP connected (OAuth)
- [x] Profile monitors live for top 5 leads
- [x] Keyword monitors live (7 active searches)
- [x] Social Listening table created in Airtable
- [x] 3 social listening leads identified + added (Karla, Jonathan, Sarah)
- [x] Domain field added to all tables
- [ ] Lead dossiers (1-pager per top 5 lead) — IN PROGRESS
- [ ] Cold email sequences written per persona
- [ ] Sequences loaded into Clay / outreach tool
- [ ] Campaign launched

## Pitch Angle for Solis Call

"The UK market hasn't discovered red light therapy yet. Only 29 LinkedIn posts in the past month mention it — dominated by Chinese manufacturers. That's the opportunity. We go outbound before the noise arrives, not after."
