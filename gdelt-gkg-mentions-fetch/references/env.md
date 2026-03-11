# Environment Variables

Use environment variables for all runtime behavior. The script does not hardcode secrets or mutable operational settings.

- `GDELT_GKG_MENTIONS_BASE_URL`
  - Default: `http://data.gdeltproject.org/gdeltv2`
  - Base URL for `lastupdate.txt`, `masterfilelist.txt`, and file downloads.
- `GDELT_GKG_MENTIONS_TIMEOUT_SECONDS`
  - Default: `60`
  - HTTP timeout for each request.
- `GDELT_GKG_MENTIONS_MAX_RETRIES`
  - Default: `4`
  - Retry count for transient failures (total attempts = retries + 1).
- `GDELT_GKG_MENTIONS_RETRY_BACKOFF_SECONDS`
  - Default: `1.5`
  - Initial retry delay in seconds.
- `GDELT_GKG_MENTIONS_RETRY_BACKOFF_MULTIPLIER`
  - Default: `2.0`
  - Exponential factor for retry delay growth.
- `GDELT_GKG_MENTIONS_MIN_REQUEST_INTERVAL_SECONDS`
  - Default: `0.4`
  - Minimum sleep interval between requests to reduce burst traffic.
- `GDELT_GKG_MENTIONS_MAX_FILES_PER_RUN`
  - Default: `20`
  - Hard safety cap for `--max-files`.
- `GDELT_GKG_MENTIONS_USER_AGENT`
  - Default: `gdelt-gkg-mentions-fetch/1.0`
  - Value sent in HTTP `User-Agent`.

Example:

```bash
export GDELT_GKG_MENTIONS_BASE_URL="http://data.gdeltproject.org/gdeltv2"
export GDELT_GKG_MENTIONS_TIMEOUT_SECONDS="60"
export GDELT_GKG_MENTIONS_MAX_RETRIES="4"
export GDELT_GKG_MENTIONS_RETRY_BACKOFF_SECONDS="1.5"
export GDELT_GKG_MENTIONS_RETRY_BACKOFF_MULTIPLIER="2.0"
export GDELT_GKG_MENTIONS_MIN_REQUEST_INTERVAL_SECONDS="0.4"
export GDELT_GKG_MENTIONS_MAX_FILES_PER_RUN="20"
export GDELT_GKG_MENTIONS_USER_AGENT="gdelt-gkg-mentions-fetch/1.0"
```
