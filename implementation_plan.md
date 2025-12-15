# Implementation Plan - Fix Hotspot Google Login

## Goal Description
Fix the issue where Google Login fails on the Hotspot due to `TCP_DENIED` errors in Squid. The goal is to identify and whitelist the necessary Google domains to allow the OAuth flow to complete.

## User Review Required
> [!IMPORTANT]
> Need to determine if changes are needed in Squid (`squid.conf`) or the firewall/IDPS logic.

## Proposed Changes
### Analysis
- Analyze the log snippets provided:
  - `accounts.google.com` (Blocked)
  - `google-ohttp-relay-safebrowsing.fastly-edge.com` (Blocked)

### Configuration
- [ ] **Squid Configuration**: Add identified domains to the allowed list (likely `dstdomain` ACLs).
- [ ] **Walled Garden**: Ensure these domains are accessible before authentication.

## Verification Plan
### Manual Verification
- Attempt Google Login from a client behind the Hotspot.
- Monitor Squid logs (`access.log`) for `TCP_DENIED` vs `TCP_MISS`/`TCP_TUNNEL`.
