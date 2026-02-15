# Outcome Log Contract

**Version:** v2.0
**Role:** Defines the schema for per-touch outcome tracking in Module 4A
**Status:** Active

---

## Purpose

Every outbound touch gets logged. The outcome log is the raw data that Module 4B (Signal Performance Updater) analyzes to update signal scores, identify winning messaging variants, and surface emerging patterns. If it takes more than 30 seconds to log an entry, it won't get used — so the schema is minimal by design.

---

## Entry Schema

Each entry represents one outbound touch (email sent, PVP delivered, follow-up sent).

```json
{
  "entry_id": "auto-generated UUID or sequential number",
  "timestamp": "ISO 8601 datetime of when the touch was sent",
  "account": {
    "company_name": "Prospect company name",
    "domain": "prospect-domain.com",
    "industry": "Industry vertical (free text, normalized by Module 4B)",
    "company_size": "Employee count or range"
  },
  "play": {
    "play_name": "Name of the play used (from Module 3A output)",
    "play_number": "Play 1 / Play 2 / Play 3"
  },
  "signal": {
    "signal_name": "Name of the signal that triggered this play",
    "signal_composite_score": 0.00,
    "signal_validation_status": "Validated / Partially Validated / Unvalidated / New"
  },
  "messaging": {
    "subject_line_variant": "A / B",
    "opening_line_variant": "A / B",
    "pvp_sent": "PVP name if sent, null if no PVP",
    "pvp_deployment": "Cold Opener / Follow-Up / Re-Engagement / null"
  },
  "outcome": {
    "result": "Reply / Meeting / No Response / Bounce / Unsubscribe / Out of Office / Referral",
    "days_to_response": null,
    "reply_sentiment": "Positive / Neutral / Negative / null",
    "meeting_booked": false,
    "pipeline_created": false,
    "pipeline_value": null
  },
  "notes": "Free text — anything notable about this interaction. Module 4B mines this field for emerging patterns."
}
```

---

## Field Reference

### Required Fields (must be filled for every entry)

| Field | Type | Description | Example |
|-------|------|-------------|---------|
| `account.company_name` | string | Prospect company | `"Acme Landscaping"` |
| `account.domain` | string | Prospect domain | `"acme-landscaping.com"` |
| `play.play_name` | string | Play that triggered the touch | `"PE Acquisition Signal Play"` |
| `signal.signal_name` | string | Signal that fired | `"PE acquisition of landscape contractor"` |
| `signal.signal_composite_score` | number | Composite score at time of send | `3.85` |
| `messaging.subject_line_variant` | enum | Which A/B variant was sent | `"A"` |
| `messaging.opening_line_variant` | enum | Which A/B variant was sent | `"B"` |
| `outcome.result` | enum | What happened | `"Reply"` |

### Optional Fields (fill when applicable)

| Field | Type | Description | When to Fill |
|-------|------|-------------|-------------|
| `account.industry` | string | Industry vertical | Always if known |
| `account.company_size` | string | Employee count or range | Always if known |
| `play.play_number` | string | Play number from Module 3A | Always |
| `signal.signal_validation_status` | enum | Validation status at time of send | Always if known |
| `messaging.pvp_sent` | string | PVP name if attached | When PVP was included |
| `messaging.pvp_deployment` | enum | How PVP was deployed | When PVP was included |
| `outcome.days_to_response` | number | Days between send and reply | When result is Reply, Meeting, or Referral |
| `outcome.reply_sentiment` | enum | Tone of the reply | When result is Reply |
| `outcome.meeting_booked` | boolean | Did a meeting get booked? | When result is Reply or Meeting |
| `outcome.pipeline_created` | boolean | Did an opportunity get created? | When known (may be filled later) |
| `outcome.pipeline_value` | number | Dollar value of opportunity | When pipeline_created is true |
| `notes` | string | Free text observations | Whenever something notable happened |

### Enum Values

| Field | Allowed Values |
|-------|----------------|
| `outcome.result` | `Reply`, `Meeting`, `No Response`, `Bounce`, `Unsubscribe`, `Out of Office`, `Referral` |
| `outcome.reply_sentiment` | `Positive`, `Neutral`, `Negative`, `null` |
| `messaging.subject_line_variant` | `A`, `B` |
| `messaging.opening_line_variant` | `A`, `B` |
| `messaging.pvp_deployment` | `Cold Opener`, `Follow-Up`, `Re-Engagement`, `null` |
| `signal.signal_validation_status` | `Validated`, `Partially Validated`, `Unvalidated`, `New` |

---

## Logging Workflow

The outcome log is designed for two input methods:

### Method 1: Direct JSON Entry
Paste or append a JSON object matching the schema above. Best for automation or batch imports.

### Method 2: Natural Language → Structured (via LLM)
Paste natural language like:
> "Sent Play 2 to Acme Landscaping (acme-landscaping.com), subject line B, opening A. PE acquisition signal, score 3.85. Included the competitive audit PVP as follow-up. Got a positive reply in 3 days, meeting booked."

An LLM (Claude or similar) parses this into the JSON schema. This is the <30 second path.

---

## Downstream Consumer

- **Module 4B (Signal Performance Updater):** Reads the full outcome log (50+ entries minimum) and produces signal score updates, variant performance analysis, and kill/promote recommendations.

---

## File Format

The outcome log is stored as a JSON array:

```json
[
  { "entry_id": "001", ... },
  { "entry_id": "002", ... }
]
```

Template file: `templates/outcome-log.json`
