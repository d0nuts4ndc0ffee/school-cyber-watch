# How School Cyber Watch is updated

The site is a static page (`index.html`) that loads three data files. A daily research run edits only these files and commits them. It never edits `index.html`.

| File | What it holds |
|---|---|
| `data/incidents.json` | Every incident, one object per incident |
| `data/changelog.json` | One entry per update run that changed something, newest first |
| `data/meta.json` | When the last run happened and whether it succeeded |

## Incident record

```json
{
  "id": "richmond-public-schools",
  "name": "Richmond Public Schools",
  "sector": "K-12",
  "place": "Richmond, Virginia",
  "country": "United States",
  "date": "2026-10-21",
  "dateApprox": false,
  "type": "ransom",
  "impacts": ["outage", "data"],
  "summary": "Two to four plain sentences in our own words.",
  "people": 26000,
  "peopleNote": "students enrolled",
  "closure": "2 school days",
  "actor": "Interlock (claimed)",
  "ransom": "$1.2 million, not paid",
  "sources": [{ "label": "WTVR CBS 6", "url": "https://..." }],
  "status": "confirmed",
  "addedAt": "2026-11-11",
  "updatedAt": "2026-11-11"
}
```

Required: `id`, `name`, `sector`, `place`, `country`, `date`, `dateApprox`, `type`, `impacts`, `summary`, `sources`, `status`, `addedAt`, `updatedAt`.
Optional (leave out when unknown, never guess): `people`, `peopleNote`, `closure`, `actor`, `ransom`.

Allowed values:
- `sector`: `K-12`, `Higher ed`, `Shared platform` (a vendor, network or department serving many schools)
- `type`: `ransom` (ransomware), `breach` (data breach), `access` (unauthorized access), `vendor` (breach at a supplier or platform), `other` (attack of unspecified kind)
- `impacts`: any of `closure` (classes or campus cancelled), `outage` (systems taken down), `data` (personal data exposed or stolen), `extortion` (ransom demand or leak threat)
- `status`: `unconfirmed` or `confirmed`
- Dates are `YYYY-MM-DD`. When only the month is known, use the 1st and set `dateApprox: true`.

## Rules for each run

1. **Scope.** Schools, school districts, colleges, universities, and platforms or authorities serving schools, anywhere in the world. The incident must be a cyberattack: ransomware, a data breach, unauthorized access, or an attack that shut down systems. Skip outages with no sign of an attack, phishing warnings with no incident, and lawsuits about old breaches unless they reveal new facts.
2. **Match before adding.** Compare each find with existing records by school name and place. If it matches, update that record and set `updatedAt`. Never create a second record for the same incident. A new `id` is the school name in lowercase with hyphens.
3. **Confirmation.** A new record starts as `unconfirmed` unless an official notice from the school or authority, or two independent outlets, report it. Promote a record to `confirmed` when that bar is met, and add the new source.
4. **Claims.** Numbers or attributions that come only from attackers are labelled "(claimed)" or "claimed by the attackers".
5. **Wording.** Summaries are written fresh in plain language. Do not copy sentences from articles.
6. **Every record keeps at least one working source link.**
7. **Changelog.** If anything changed, add one entry at the top of `changelog.json`: `{"date", "summary", "added": [ids], "updated": [ids]}`. The summary is one or two sentences, e.g. "Added 2 incidents, including a ransomware closure in Ohio. Richmond confirmed its leaked data includes staff SSNs."
8. **Meta.** Always update `meta.json`: `lastRunAt` (ISO time), `lastRunStatus` (`ok` or `failed`), `lastRunSummary`.
9. **Validate** that each file parses as JSON and every record has the required fields before committing.
10. **Commit message:** `Daily update YYYY-MM-DD: +N new, M updated` (or `no changes`).
