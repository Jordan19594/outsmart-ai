# MT Solar — Pipeline Proof of Concept

## Date: 2026-03-03

## What We Proved
End-to-end: EPC Register → Companies House → Directors — all free public data, seconds per lookup.

## Test Results

### EPC Query
- **Filters:** energy-band D-G, floor-area xl+xxl (300m²+), from-year 2022, postcode SE
- **Results:** 50 buildings returned
- **Key learning:** API requires a location filter (postcode/address/local-authority). No location = empty response.

### Cross-Match Results

| Building | Size | Rating | Company | Director(s) | Company # |
|----------|------|--------|---------|-------------|-----------|
| The Foundry, SE11 5RR | 5,355 m² | D | ILC UK Services Ltd | Paul Green, Glyn Ryland | 06666320 |
| 34 Bowater Rd, SE18 5TF | 4,905 m² | E | Knitshop Ltd | Julieta Brandao | 05521913 |
| Unit 6 Kellner Rd, SE28 0AX | 1,112 m² | D | Max Motors Garage Ltd | Maxwell Amegadzie | 12579266 |
| 8 Kellner Rd, SE28 0AX | 1,112 m² | D | B&R Distribution Ltd | Nazma Bibi | 08719153 |
| 17 Oval Way, SE11 5RR | 5,355 m² | D | Full Fact (charity) | 10 directors | 06975984 |

### Filtering Insights
- Filter out `company_type=private-limited-guarant-nsc*` to exclude charities/non-profits
- Shared buildings (The Foundry) have multiple tenants — all discoverable
- Industrial estates (Kellner Rd) cluster multiple businesses at same postcode

## Pipeline Architecture (Validated)

```
1. EPC Register API
   GET /api/v1/non-domestic/search
   ?energy-band=d,e,f,g
   &floor-area=xl,xxl
   &from-year=2022
   &postcode={prefix}
   &size=5000
   → Qualified buildings with address, floor area, rating, type

2. Companies House API
   GET /advanced-search/companies
   ?location={postcode}
   &company_status=active
   → Companies registered at that address

3. Companies House Officers API
   GET /company/{number}/officers
   → Current directors (filter: no resigned_on, role=director)

4. Enrichment Waterfall
   Director name + company → LinkedIn → Prospeo/BlitzAPI → verified email

5. Score & Rank
   floor_area × energy_band × company_type = lead tier
```

## London/SE Postcode Prefixes to Cover
SE, SW, EC, E, N, NW, W, WC, CR, BR, DA, TN, KT, SM, RM, EN, HA, UB, TW, SL, RH, GU, ME, CT, BN, OX, MK

## Next Steps
1. Build automated script to loop all postcode prefixes
2. Filter out charities (company_type check)
3. Deduplicate shared buildings (multiple companies at same address)
4. Enrich directors via LinkedIn + Prospeo
5. Score leads: big roof + bad EPC + verified email + right person = Tier A
