---
name: easybill
description: >
  Read invoicing and accounting data (documents, customers, positions, payments)
  from the easybill REST API.
type: api
---

# EasyBill

easybill is a German cloud invoicing and accounting service. This connector reads business data from the easybill REST API: documents (invoices, offers, credit notes), customers, positions (products/articles), payments, projects, tasks, time tracking and related resources.

Base URL: `https://api.easybill.de/rest/v1`

## Authentication

### API Key
- Client app required: no
- Header format: `Authorization: Bearer ${api_key}`
- The key is generated in the easybill app under **Settings → API**.
- easybill also accepts HTTP Basic Auth (`base64(email:api_key)`), but this connector uses the bearer-token form.

## Post-Auth Steps

None required. The API key is a static credential — there is no OAuth flow, token exchange, or resource-selection step.

## Available Endpoints

All endpoints are read-only (`GET`) paginated collections.

| Endpoint | Method | Description |
|----------|--------|-------------|
| `documents` | GET | Invoices, offers, credit notes and other documents (incremental on `edited_at`) |
| `customers` | GET | Customers / clients |
| `customer-groups` | GET | Customer groups |
| `positions` | GET | Positions (products / articles) |
| `position-groups` | GET | Position groups |
| `projects` | GET | Projects |
| `document-payments` | GET | Payments recorded against documents |
| `attachments` | GET | File attachments |
| `discounts-position` | GET | Position discounts |
| `discounts-position-group` | GET | Position-group discounts |
| `sepa-payments` | GET | SEPA direct-debit payments |
| `serial-numbers` | GET | Serial numbers |
| `stocks` | GET | Stock entries (per-position stock movements) |
| `tasks` | GET | Tasks |
| `text-templates` | GET | Reusable text templates |
| `time-trackings` | GET | Time-tracking entries |

## Rate Limits

- 10 requests per 60 seconds (the conservative floor used by this connector).
- easybill enforces per-plan limits: **PLUS** = 10 requests/minute, **BUSINESS** = 60 requests/minute. Exceeding the limit returns `429 Too Many Requests`.

## Caveats

- Pagination is page-number based (`page`, `limit`; default 100, max 1000 per page). The list envelope is `{ page, pages, limit, total, items: [...] }` — records live under `items`.
- Only the `documents` endpoint exposes a true last-modified filter (`edited_at`) and supports incremental replication. All other endpoints are full-refresh only.
- Dates use the `Europe/Berlin` timezone: `date` = `Y-m-d`, `date-time` = `Y-m-d H:i:s`. Some date/date-time fields may be `null` on older records.
- Nested object and array fields (e.g. `address`, `items`, `customer_snapshot`) are mapped to the JSON canonical type.
- List filters accept comma-separated or array (`field[]=a&field[]=b`) syntax; the HTTP request line is capped at 4094 bytes (`414` on overflow).
