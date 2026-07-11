> This repo contains the public manifest/docs for a hosted service — the full implementation runs in production as part of Velora.

# Velora — Argentine Commerce MCP

**A hosted, multi-capability commerce toolkit for Argentina — real AFIP invoicing, MercadoPago, logistics, catalog and WhatsApp behind one authenticated endpoint.**

Velora is a hosted [Model Context Protocol](https://modelcontextprotocol.io) server that gives AI agents tools to operate commerce in Argentina: fiscal invoicing (AFIP/ARCA), payments (MercadoPago), logistics (Andreani / PedidosYa), catalog, cash register, and WhatsApp messaging — authenticated and tenant-isolated.

> Other AFIP MCP servers exist (some emit invoices, some only search the docs). Velora's angle is the **bundle**: fiscal is one of several commerce rails — payments, logistics, catalog, messaging — exposed through a single hosted, authenticated endpoint, so an agent runs a whole Argentine business, not just invoicing.

## Endpoint

```
https://tools.somosvelora.com/api/mcp
```

Transport: streamable HTTP. Auth: API key (per-tenant HMAC) or OAuth 2.1 (WorkOS bearer). Any MCP client — Claude, OpenAI, Gemini, Cursor, or your own agent — consumes the same endpoint.

**Free during early access.** No usage cost while we onboard the first builders — the fiscal and pure tools run no LLM on our side, so it stays cheap to keep open.

## Connect

Point any MCP client that supports a remote **streamable HTTP** server at the endpoint, with your credentials as headers:

```jsonc
{
  "mcpServers": {
    "velora": {
      "type": "http",
      "url": "https://tools.somosvelora.com/api/mcp",
      "headers": {
        "X-API-Key": "<your-key>",
        "X-Business-Id": "<your-business-id>"
      }
    }
  }
}
```

The exact config key (`type`/`transport`) varies by client — use your client's remote/HTTP MCP server format. Request access at [somosvelora.com](https://somosvelora.com).

The pure fiscal helpers (`validate_cuit`, `split_iva`, and the rest) are also available **without authentication** at `https://tools.somosvelora.com/api/mcp/public`.

## Tool packs

| Pack | What it does |
|------|--------------|
| **Fiscal (AFIP/ARCA)** | `emit_invoice` (real CAE — Factura A/B/C + Consumidor Final), `consultar_comprobante` / `consultar_padron` (invoice + taxpayer lookup), `get_fiscal_readiness`. `emit_nota` (NC/ND): **sandbox only today — real credit/debit-note emission is in progress.** |
| **Pure (always on)** | `validate_cuit`, `validate_cuil`, `validate_cbu`, `build_afip_qr`, `format_ars`, `to_afip_date`, `split_iva`, `describe_invoice_type`, `parse_ar_amount` |
| **Payments** | MercadoPago links, tracked payments, payment-intent status |
| **Logistics** | quote / create / track shipments (Andreani, PedidosYa) |
| **Catalog & Stock** | create / edit / delete products, stock load, bulk price update |
| **Sales & Cash** | register sales, returns, cash-register movements and balance |
| **Customers & Suppliers** | find / upsert customers, supplier management |
| **Messaging** | WhatsApp text & template sends |
| **Reports** | `query_sales` — sales analytics |
| **Connection** | `connection_status` — per-integration setup state (call this first) |

> **Maturity / what's live.** `emit_invoice` (Factura A/B/C + Consumidor Final), the lookup tools, and the Pure tools are production-verified against real AFIP. `emit_nota` (NC/ND) currently emits in **sandbox only** — real credit/debit-note emission is not yet wired (it returns `sandbox: true`, never a fake CAE). Payments, Logistics and Messaging are real tools that require **per-tenant credential onboarding** (connect MercadoPago / Andreani / WhatsApp) — until a tenant connects them they return demo data or fail gracefully, so don't wire them into a production flow before onboarding. `emit_invoice` issues a real CAE only once ARCA onboarding is complete (check with `get_fiscal_readiness`); otherwise it returns a clearly-flagged sandbox response (`sandbox: true`), never a fake CAE presented as real.

Full machine-readable catalog: [`tools.somosvelora.com/llms.txt`](https://tools.somosvelora.com/llms.txt)

## Why a managed layer

AFIP's web services (WSAA + WSFE) are notoriously painful: attached PKCS#7 CMS signing, weak-DH TLS handshakes, per-CUIT certificate and token management, and a stream of new resolutions (e.g. RG 5616). Velora absorbs all of it behind clean, agent-native tool calls so you don't reimplement the fiscal stack — the same reason developers reach for managed layers over government tax APIs elsewhere in LATAM.

## Status

- ✅ Real AFIP invoicing verified in production (live CAE issuance for an onboarded tenant).
- ✅ MercadoPago payments and the pure validation/formatting tools are live.
- 🔧 Logistics & messaging require per-tenant credential onboarding (see **Maturity** above).

## Links

- Site: [somosvelora.com](https://somosvelora.com)
- Toolkit endpoint: [tools.somosvelora.com](https://tools.somosvelora.com)
- Blog (AFIP integration field notes): [somosvelora.com/blog](https://somosvelora.com/blog)

---

This repository is the public manifest and documentation for the Velora MCP server. The server itself is hosted; this repo carries the `server.json` registry manifest and connection docs.
