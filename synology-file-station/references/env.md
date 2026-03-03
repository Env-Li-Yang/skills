# Environment Variables

## Required
- `SYNOLOGY_BASE_URL`: DSM service address, for example `https://nas.example.com:5001`.
- `SYNOLOGY_USERNAME`: DSM username.
- `SYNOLOGY_PASSWORD`: DSM password.

## Optional
- `SYNOLOGY_VERIFY_SSL`: `true`/`false`, default `true`.
- `SYNOLOGY_TIMEOUT`: HTTP timeout in seconds, default `30`.
- `SYNOLOGY_SESSION`: login session name, default `FileStation`.
- Compatibility aliases:
  - `SYNOLOGY_URL` (same as `SYNOLOGY_BASE_URL`)
  - `SYNOLOGY_USER` (same as `SYNOLOGY_USERNAME`)
  - `SYNOLOGY_PASS` (same as `SYNOLOGY_PASSWORD`)

## Quick Setup
```bash
cp assets/config.example.env .env
# edit .env with real values
set -a
source .env
set +a
```

## Sanity Check
```bash
python3 scripts/synology_file_station.py check-config
```

## Remote Probe (optional)
```bash
python3 scripts/synology_file_station.py check-config --probe
```
