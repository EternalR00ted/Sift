# Sift

Sift signal from noise.

An LLM-augmented triage layer for Microsoft Sentinel alerts.

Sift sits between Sentinel and the analyst. It reads an incoming alert, asks Claude for a first-pass assessment, and posts the result back to the alert as a comment: revised severity, confidence, likely ATT&CK technique, recommended next step, and the reasoning behind it. The analyst sees the enrichment before they open the alert. Sift never closes or escalates anything on its own.

Most SOC triage is pattern-matching against context you've seen before. That's the part worth handing off.

## Status

Pre-alpha. Week 1 of about 6.

The pipeline runs end to end against synthetic alert fixtures. No real Sentinel data yet — that lands in Week 3.

## Updates

Running log of what actually shipped each week. Newest first.

<!--
Format:

### YYYY-MM-DD — Week N
- What landed
- What broke
- What's next
-->



## How it works

Three contracts:

1. **Alert** — validated input, pulled from `/security/alerts_v2`.
2. **The LLM call** — system prompt plus alert context, structured response via Claude tool use.
3. **TriageVerdict** — validated output, posted to `/security/alerts_v2/{id}/comments`.

Pydantic enforces the boundaries. Every call is logged with full input and output so verdicts can be replayed and reviewed later.

## Stack

- Python 3.12+
- `anthropic` — Claude API client
- `pydantic` v2 — data models, structured output via tool use
- `httpx` — Microsoft Graph Security API client
- `uv` — project and dependency management
- `pytest` — tests
- `ruff` — lint and format

## Running it

Currently runs against synthetic fixtures only.

```bash
git clone https://github.com/eternalr00ted/sift.git
cd sift
uv sync
cp .env.example .env   # add your ANTHROPIC_API_KEY
uv run python -m sift.cli triage --alert samples/alert_001.json
```

Prints a structured `TriageVerdict` to stdout.

## Roadmap

- **Week 1** — Vertical slice. Load a synthetic alert, call Claude, print the response.
- **Week 2** — Structured output. Pydantic models, tool-use schemas, parsed `TriageVerdict`.
- **Week 3** — Sentinel integration. Graph Security API client, real alert ingestion.
- **Week 4** — Write-back. Verdicts posted as alert comments.
- **Week 5** — Tests and CI. Unit and integration tests, GitHub Actions.
- **Week 6** — Polish. Docs, sample alerts, logging hooks.

## On AI assistance

I used Claude to think through the design before writing anything. The code is mine.

## License

Apache 2.0. See [LICENSE](LICENSE).

## About

Built by Khalid Ozal — SOC analyst and detection engineer working in Microsoft security and LLM-augmented defense. More writing at [Eternalr00ted](https://eternalr00ted.com).
