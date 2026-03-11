---
name: gdelt-gkg-mentions-fetch
description: Fetch GDELT 2.0 GKG and Global Mentions export snapshots from lastupdate/masterfilelist with retry, throttling, transport validation, and structure validation. Use when tasks need latest or time-range GKG files (*.gkg.csv.zip) or mentions files (*.mentions.CSV.zip) for deterministic ingestion, transfer checks, and machine-readable manifests.
---

# GDELT GKG / Mentions Fetch

## Core Goal
- Fetch GDELT 2.0 `GKG` table exports (`*.gkg.csv.zip`).
- Fetch GDELT 2.0 `Global Mentions` table exports (`*.mentions.CSV.zip`).
- Resolve latest available snapshot via `lastupdate.txt`.
- Resolve historical snapshots in a UTC range via `masterfilelist.txt`.
- Persist downloaded files and return machine-readable JSON manifest.
- Keep runtime observable with structured logs and optional log file.

## Required Environment
- Configure runtime by environment variables (see `references/env.md`).
- Start from `assets/config.example.env`.
- Load env values before running commands:

```bash
set -a
source assets/config.example.env
set +a
```

## Workflow
1. Validate effective configuration.

```bash
python3 scripts/gdelt_gkg_mentions_fetch.py check-config --pretty
```

2. Inspect the latest available file for one dataset.

```bash
python3 scripts/gdelt_gkg_mentions_fetch.py resolve-latest \
  --dataset gkg \
  --pretty
```

3. Dry-run a historical range selection before downloading.

```bash
python3 scripts/gdelt_gkg_mentions_fetch.py fetch \
  --dataset mentions \
  --mode range \
  --start-datetime 20260301000000 \
  --end-datetime 20260301120000 \
  --max-files 3 \
  --dry-run \
  --pretty
```

4. Fetch files with transport and structure validation.

```bash
python3 scripts/gdelt_gkg_mentions_fetch.py fetch \
  --dataset gkg \
  --mode latest \
  --max-files 1 \
  --output-dir ./data/gdelt-gkg \
  --preview-lines 2 \
  --validate-structure \
  --expected-columns 27 \
  --quarantine-dir ./data/gdelt-gkg-quarantine \
  --log-level INFO \
  --log-file ./logs/gdelt-gkg-fetch.log \
  --pretty
```

## Built-in Robustness
- Apply retry with exponential backoff on transient HTTP/network failures.
- Respect `Retry-After` when present on retriable responses.
- Throttle request frequency with a minimum interval between requests.
- Enforce `--max-files` safety cap (`GDELT_GKG_MENTIONS_MAX_FILES_PER_RUN`) to prevent accidental bulk pulls.
- Validate datetime format and range boundaries before remote calls.
- Validate transport and structure after download:
  - ZIP CRC/integrity check
  - UTF-8 strict decoding check
  - Tab column-count check
  - Optional bad-line issue quarantine (`--quarantine-dir`)
- Emit JSON results while writing operational logs to stderr and optional log file.

## Dataset Defaults
- `mentions`
  - File pattern: `YYYYMMDDHHMMSS.mentions.CSV.zip`
  - Default expected columns: `16`
- `gkg`
  - File pattern: `YYYYMMDDHHMMSS.gkg.csv.zip`
  - Default expected columns: `27`

If GDELT changes schema or publishes variant files, override with `--expected-columns` and document the reason in the calling workflow.

## Scope Decision
- Keep one atomic file-table fetch implementation shared by two datasets: `GKG` and `Global Mentions`.
- Keep atomic operations only; do not add internal scheduler/polling loops.

## References
- `references/gdelt-data-sources.md`
- `references/gdelt-limitations.md`
- `references/gdelt-schemas.md`
- `references/env.md`

## Script
- `scripts/gdelt_gkg_mentions_fetch.py`

## OpenClaw Invocation Compatibility
- Keep skill trigger metadata in `name`, `description`, and `agents/openai.yaml`.
- Invoke in prompts with `$gdelt-gkg-mentions-fetch`.
- Keep the skill atomic: only resolve/fetch on demand.
- Use script parameters for fetch conditions (`--dataset`, `--mode range`, `--start-datetime`, `--end-datetime`).
- If you need polling, let OpenClaw orchestrate repeated invocations externally, not inside this skill.
