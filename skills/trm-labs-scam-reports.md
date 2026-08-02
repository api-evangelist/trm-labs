---
name: Look up and contribute Chainabuse scam reports
description: Query the Chainabuse Public API for scam reports on an address, token, or domain, retrieve a single report, and contribute new reports in batch.
api: openapi/trm-labs-chainabuse-openapi.yml
operations: [GetReports, Report, CreateReports]
---

# Look up and contribute Chainabuse scam reports

Check whether a blockchain address, token, or domain has been reported as
malicious, read a specific report, and submit new abuse reports.

## Auth
- HTTP Basic against `https://api.chainabuse.com/v0`. Generate credentials from
  your Chainabuse user profile.
- Standard tier: 1 req/sec, 100 req/day. Request enhanced limits via API key.

## Steps
1. **Search reports** — `GetReports`
   `GET /reports?address=<addr>&chain=<ChainKind>&category=<ScamCategoryKind>`
   Page with `page` (default 1) and `perPage` (default 50, max 50); order with
   `orderByField` (`CREATED_AT` | `SUBMITTED_BY`) and `orderByDirection`
   (`ASC` | `DESC`). Response is `{ reports: ReportPayload[], count }`.
2. **Read one report** — `Report`
   `GET /reports/{reportId}` returns the full `ReportPayload` (addresses, losses,
   accused scammers, evidences, ips).
3. **Contribute reports** — `CreateReports`
   `POST /reports/batch` with a body containing required `addresses`,
   `description`, and `scamCategory`, plus optional `tokens`, `transactionHashes`,
   `losses`, `evidences`, and `accusedScammers`. Response returns `createdReports`
   and `failedReports` (with `error` + `reportIndex`).

## Rules
- Use page-number pagination; never assume more than `perPage=50` per call.
- On `422`/`403` inspect the `{ reason }` field before retrying.
- Do not submit unverified accusations as fact; populate `evidences` where possible.
