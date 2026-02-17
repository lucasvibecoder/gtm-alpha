# runs/

Output directory for GTM Alpha client work. One folder per vendor domain.

```
runs/
├── README.md              ← this file (public)
├── bobyard.com/
│   ├── brief.md
│   ├── play-design-2026-02-15.md
│   ├── execution-handoff.md
│   └── pvps/
│       ├── amelandscape.com-estimator-capacity-audit.md
│       └── techscapeinc.com-bid-radar-brief.md
├── ramp.com/
│   ├── brief.md
│   └── pvps/...
└── ...
```

## Convention

- Folder name = vendor domain (e.g., `bobyard.com`, `ramp.com`)
- `brief.md` — Module 1A output
- `play-design-{date}.md` — Module 3A output
- `execution-handoff.md` — Execution handoff doc
- `call-synthesis-{date}.md` — Module 2B output
- `reconciliation-{date}.md` — Module 2C output
- `performance-update-{date}.md` — Module 4B output
- `pvps/` — Module 3B deliverables, named `{prospect-domain}-{pvp-type}.md`

## Privacy

All content under `runs/` (except this README) is gitignored. Client work stays local.
