# Velora — Argentine Commerce MCP

**The only MCP server that *executes* real AFIP/ARCA electronic invoicing — not just docs or search.**

Velora is a hosted [Model Context Protocol](https://modelcontextprotocol.io) server that gives AI agents production-grade tools to operate commerce in Argentina: fiscal invoicing (AFIP/ARCA), payments (MercadoPago), logistics (Andreani / PedidosYa), catalog, cash register, and WhatsApp messaging — all authenticated and tenant-isolated.

> Existing AFIP MCP servers only **search the documentation**. Velora is the only one that issues a real **CAE** (the AFIP authorization code) against production web services.

## Endpoint

```
https://tools.somosvelora.com/api/mcp
```

Transport: streamable HTTP. Auth: API key (per-tenant HMAC) or OAuth 2.1 (WorkOS bearer). Any MCP client — Claude, OpenAI, Gemini, or your own agent — consumes the same endpoint.

## Connect (Claude Desktop / any MCP client)

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

Request access at [somosvelora.com](https://somosvelora.com).

## Tool packs

| Pack | What it does |
|------|--------------|
| **Fiscal (AFIP/ARCA)** | `emit_invoice` (real CAE), `emit_nota` (credit/debit note), `get_fiscal_readiness` |
| **Pure (always on)** | `validate_cuit`, `validate_cuil`, `validate_cbu`, `build_afip_qr`, `format_ars`, `to_afip_date`, `split_iva`, `describe_invoice_type`, `parse_ar_amount` |
| **Payments** | MercadoPago links, tracked payments, payment-intent status |
| **Logistics** | quote / create / track shipments (Andreani, PedidosYa) |
| **Catalog & Stock** | create / edit / delete products, stock load, bulk price update |
| **Sales & Cash** | register sales, returns, cash-register movements and balance |
| **Customers & Suppliers** | find / upsert customers, supplier management |
| **Messaging** | WhatsApp text & template sends |

Full machine-readable catalog: [`tools.somosvelora.com/llms.txt`](https://tools.somosvelora.com/llms.txt)

## Why a managed layer

AFIP's web services (WSAA + WSFE) are notoriously painful: attached PKCS#7 CMS signing, weak-DH TLS handshakes, per-CUIT certificate and token management, and a stream of new resolutions (e.g. RG 5616). Velora absorbs all of it behind clean, agent-native tool calls so you don't reimplement the fiscal stack — the same reason developers reach for managed layers over government tax APIs elsewhere in LATAM.

## Status

- ✅ Real AFIP invoicing verified in production (live CAE issuance).
- ✅ MercadoPago payments and the pure validation/formatting tools are live.

## Links

- Site: [somosvelora.com](https://somosvelora.com)
- Toolkit endpoint: [tools.somosvelora.com](https://tools.somosvelora.com)
- Blog (AFIP integration field notes): [somosvelora.com/blog](https://somosvelora.com/blog)

---

This repository is the public manifest and documentation for the Velora MCP server. The server itself is hosted; this repo carries the `server.json` registry manifest and connection docs.
