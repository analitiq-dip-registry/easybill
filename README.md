# EasyBill

Read invoicing and accounting data from [easybill](https://www.easybill.de) — documents (invoices, offers, credit notes), customers, positions, payments, projects and more — via the easybill REST API.

## What is this?

This is a **connector** — a configuration that defines how to authenticate with easybill and what data endpoints are available for reading and writing. It does not move data by itself. Instead, it is used by the [Analitiq](https://analitiq-app.com) data integration platform or the open-source `analitiq-dip-registry` engine to set up data pipelines.

## How to use this connector

There are two ways to use this connector:

### Option 1 — Analitiq Cloud (no setup required)

All connectors from this registry are automatically available on [analitiq-app.com](https://analitiq-app.com). Simply log in, select the connector, and follow the on-screen instructions to connect your account.

### Option 2 — Open Source (self-hosted)

All connectors are open source and free to use. To get started:

1. Clone the [analitiq-dip-registry](https://github.com/analitiq-dip-registry) repository
2. Install the Claude plugin `analitiq-plugin-dataflow`
3. Launch Claude in the root directory of `analitiq-dip-registry`
4. Tell it: *"I need to move data from X to Y"*

The `analitiq-plugin-dataflow` plugin will automatically fetch the required connectors from the [Analitiq DIP Registry](https://github.com/analitiq-dip-registry) and set up the data flow pipeline for you.

## Prerequisites

- An easybill account on a plan that includes API access (the **PLUS** or **BUSINESS** tier).
- An easybill REST API key generated from your account settings.

## Authentication

This connector authenticates with a single **API key**, sent as a bearer token in the `Authorization: Bearer <api_key>` header on every request. No OAuth flow, client app, or post-authentication setup is required.

(easybill also accepts HTTP Basic Auth using your email and API key, but this connector uses the simpler bearer-token form.)

### How to get your credentials

1. Log in to your account at [easybill.de](https://www.easybill.de).
2. Open **Settings → API** (German: *Einstellungen → API*).
3. Generate an API key and copy it.
4. Paste it into the connector's **API Key** field when prompted.

## Available Endpoints

The table below lists all data endpoints defined by this connector. Each endpoint represents a resource you can read from or write to.

All endpoints are read-only (`GET`) collections and are paginated (page-number style, up to 1000 records per page).

| Endpoint | Method | Description |
|----------|--------|-------------|
| `documents` | GET | Invoices, offers, credit notes and other documents (supports incremental sync on `edited_at`) |
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

## Limitations

- **Rate limits** — easybill enforces a per-plan request limit: **PLUS** allows 10 requests/minute, **BUSINESS** allows 60 requests/minute. Exceeding it returns `429 Too Many Requests`. This connector throttles to the conservative floor of **10 requests/minute** so it works on every plan; raise it if your account is on the BUSINESS tier.
- **Result size** — list responses return 100 records per page by default; this connector requests the maximum of 1000 per page.
- **Incremental sync** — only the `documents` endpoint exposes a true last-modified filter (`edited_at`) and supports incremental replication. All other endpoints are full-refresh only.
- **Dates & timezone** — easybill emits dates in the `Europe/Berlin` timezone (`date` as `Y-m-d`, `date-time` as `Y-m-d H:i:s`). Some date fields may be `null` for older records.

## For AI agents

This connector includes `CLAUDE.md` and `AGENTS.md` files — machine-readable references used by AI agents and agentic frameworks. They document authentication types, available endpoints, post-auth steps, and any caveats for programmatic use. Both files are kept identical — `CLAUDE.md` is for Claude Code, `AGENTS.md` is for other agent frameworks.

## Create a connector to any system

You can create a new connector to any API or database using Claude and the Analitiq connector builder plugin:

1. Install [Claude Code](https://claude.ai/code)
2. Install the connector builder plugin:
   ```
   claude plugin add analitiq-dip-registry/analitiq-plugin-connector-builder
   ```
3. Launch Claude and say: *"I want to create a connector for [system name]"*
4. The plugin will interview you about the system, research its API documentation, and generate the full connector with all required files

No coding required — the plugin handles authentication research, endpoint schema generation, and file creation automatically.

![Example of Claude building a connector](media/example_1.png)

## Contributing

All connectors in this registry are community-maintained and live at [github.com/analitiq-dip-registry](https://github.com/analitiq-dip-registry). To add new endpoints or improve an existing connector, install the [connector builder plugin](https://github.com/analitiq-dip-registry/analitiq-plugin-connector-builder) and follow its instructions.

## Links

- [API Documentation](https://www.easybill.de/api)
- [Analitiq Cloud](https://analitiq-app.com)
- [Analitiq Engine (open source)](https://github.com/analitiq-ai/analitiq-engine)
- [Analitiq DIP Registry (open source)](https://github.com/analitiq-dip-registry)