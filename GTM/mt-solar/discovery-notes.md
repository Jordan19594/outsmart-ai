# MT Solar — Discovery Notes

**Client:** MT Solar (mtsolar.co.uk)
**Service:** Commercial solar installations, UK
**Date:** 2026-03-03

## Strategy: Dual Pipeline Prospecting Engine

Two parallel pipelines that cross-match:
1. **Property-first** — Find suitable commercial buildings, then identify occupants
2. **Business-first** — Find relevant companies, then qualify their premises

## Target Property Profile
- All commercial: warehouses, industrial, offices, retail, hospitality
- Large flat roofs preferred (warehouses/logistics = highest value)

## Target Personas
- Facility / Estates Managers
- MD / Owner / Directors (especially SMEs)

## Data Sources (All Free/Public)

### 1. EPC Register (Energy Performance Certificates)
- Free API — covers England & Wales
- Filters: Rating D-G (poor energy = strong solar incentive), floor area 500m²+, recent (last 2-3 years)
- Data: energy rating, floor area, building type, address, improvement recommendations
- **Key signal:** EPC recommendations that mention renewables/solar

### 2. Ordnance Survey (OS OpenData)
- Building footprint polygons → estimate roof area in m²
- Address matching/validation across datasets
- Land use classification

### 3. Companies House
- Free API — company details, directors, SIC codes
- For SMEs: pull director names directly → enrich via LinkedIn/Prospeo
- Match registered/trading addresses to EPC properties

### 4. Planning Portal
- Local authority planning applications
- Filter for solar/energy related submissions (competitors installing nearby = market signal)

### 5. Google Maps / OpenWebNinja
- Local Business Data API — phone, email, website for businesses at target addresses
- `extract_emails_and_contacts=true` for website scraping

## Decision Maker Enrichment (Waterfall)
1. Companies House → pull director names (SMEs)
2. LinkedIn → match person by company + title (Facility Manager, MD, Owner)
3. Prospeo / BlitzAPI → verified email
4. OpenWebNinja → fallback (website contact scraping)

## Outreach Strategy
- **Primary:** Cold email via Instantly
- **On positive reply:** Enrich with phone number (if not already captured) for follow-up call
- No LinkedIn or multi-channel initially

## Lead Scoring (Balanced)
Equal weight to:
- **Property quality:** EPC rating (D-G), roof area (m²), building type, age
- **Contact quality:** Verified email, title match, company size

## Geographic Scope
- Start with 2-3 regions near MT Solar's base
- Prove the model, then expand nationwide
- EPC register covers England & Wales (Scotland separate)

## Pipeline Flow (High Level)
```
EPC Register ──→ Filter (D-G, 500m²+, recent) ──→ Qualified Properties
                                                        │
OS Data ──→ Roof area estimation ──────────────────────→ ├──→ Cross-match
                                                        │
Companies House ──→ Company lookup by address ─────────→ ├──→ Matched Leads
Google Maps/OWN ──→ Business at location ──────────────→ │
                                                        ▼
                                                  Enrichment Waterfall
                                                  (CH → LinkedIn → Prospeo → OWN)
                                                        │
                                                        ▼
                                                  Scored & Tiered Leads
                                                        │
                                                        ▼
                                                  Cold Email (Instantly)
                                                        │
                                                  On reply → Phone enrichment
```
