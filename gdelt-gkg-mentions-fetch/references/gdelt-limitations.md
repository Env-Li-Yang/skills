# GDELT GKG / Mentions Fetch Constraints and Safety Notes

This skill uses public GDELT 2.0 file endpoints for `Global Mentions` and `GKG` exports.

## Constraints Confirmed from Official Documentation

- Update cadence:
  - GDELT 2.0 updates core feeds every 15 minutes.
- File granularity:
  - `lastupdate.txt` lists current snapshots by table.
  - `masterfilelist.txt` is the complete historical index and can be very large.
- Table format:
  - Mentions and GKG exports are ZIP-compressed tab-delimited files.

## Request Limits

The reviewed official GDELT pages and public index endpoints do not publish a clear numeric QPS, per-IP quota, or daily download ceiling for `lastupdate.txt`, `masterfilelist.txt`, `*.mentions.CSV.zip`, or `*.gkg.csv.zip`.

Because no official numeric request ceiling was confirmed, this skill applies client-side protections:

- Configurable timeout (`GDELT_GKG_MENTIONS_TIMEOUT_SECONDS`)
- Retries with exponential backoff (`GDELT_GKG_MENTIONS_MAX_RETRIES`, `GDELT_GKG_MENTIONS_RETRY_BACKOFF_*`)
- Request throttling (`GDELT_GKG_MENTIONS_MIN_REQUEST_INTERVAL_SECONDS`)
- Safety cap on selected files (`GDELT_GKG_MENTIONS_MAX_FILES_PER_RUN`)
- Dry-run mode before download (`fetch --dry-run`)
- Transport and structure validation after download:
  - ZIP CRC check
  - UTF-8 strict decode validation
  - Column-count validation (`--expected-columns`)
  - Optional issue quarantine (`--quarantine-dir`)
- Atomic invocation design: no internal polling loop in this skill.

## Practical Caveats

- `masterfilelist.txt` is large and full-file scanning is linear; use small time windows when possible.
- GKG rows can be very wide, so preview lines are useful but should stay small.
- Schema assumptions are based on official codebooks plus live-file inspection; if GDELT changes field counts, callers should override `--expected-columns` and document the reason.
