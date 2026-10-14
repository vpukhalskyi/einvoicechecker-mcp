<p align="center"><img src="logo.png" width="128" height="128" alt="E-Invoice Checker MCP"></p>

# E-Invoice Checker MCP server

Remote [Model Context Protocol](https://modelcontextprotocol.io) server for **European e-invoices**: detect, parse, validate and render
XRechnung, ZUGFeRD, Factur-X, Peppol BIS Billing 3.0, generic EN 16931 UBL/CII and Polish KSeF FA(3) invoices with the official
KoSIT, OpenPeppol and KSeF rule sets. Same engine as [einvoicechecker.com](https://einvoicechecker.com).

- **Endpoint:** `https://einvoicechecker.com/v1/mcp` (Streamable HTTP, stateless)
- **Auth:** optional API key, `Authorization: Bearer ek_…` ([free key](https://einvoicechecker.com/developers/), 100 requests/month). Without a key the anonymous rate limit applies (20 requests/min per IP).
- **Registry:** `com.einvoicechecker/einvoice` in the [official MCP registry](https://registry.modelcontextprotocol.io/v0.1/servers?search=einvoicechecker)
- **Docs:** https://einvoicechecker.com/mcp/

## Tools

| Tool | Does | Cost |
|---|---|---|
| `detect_invoice` | Format, syntax, profile, country standard of a file | 1 |
| `parse_invoice` | Invoice as normalised JSON (parties, lines, VAT breakdown, totals, payment) | 1 |
| `validate_invoice` | Official rules: KoSIT (XRechnung 3.0.2 incl. BR-DE), OpenPeppol BIS 3.0.20, KSeF FA(3). Every failed rule with id, severity, XPath | 1 |
| `render_invoice_html` | Readable A4 document as HTML (en, de, fr, pl, nl) | 1 |
| `render_invoice_pdf` | Readable PDF, returned as a base64 resource | 2 |
| `my_usage` | Plan, quota, usage, subscription of the connected key | 0 |
| `list_supported_formats` | Formats, rule sets, plans | 0 |

Input: `content` (invoice XML as text) or `content_base64` (ZUGFeRD / Factur-X PDF or any invoice file, base64). Files up to 10 MB.
Invoices are processed in memory and discarded; nothing from the invoice content is stored.

## Connect

**Claude Code**

```bash
claude mcp add --transport http einvoicechecker https://einvoicechecker.com/v1/mcp \
  --header "Authorization: Bearer ek_YOUR_KEY"
```

**Claude.ai / Claude Desktop:** Settings → Connectors → Add custom connector → `https://einvoicechecker.com/v1/mcp`.

**Cursor / Windsurf / VS Code** (`mcp.json`):

```json
{
  "mcpServers": {
    "einvoicechecker": {
      "url": "https://einvoicechecker.com/v1/mcp",
      "headers": { "Authorization": "Bearer ek_YOUR_KEY" }
    }
  }
}
```

**stdio clients** (via [mcp-remote](https://www.npmjs.com/package/mcp-remote)):

```json
{
  "mcpServers": {
    "einvoicechecker": {
      "command": "npx",
      "args": ["-y", "mcp-remote", "https://einvoicechecker.com/v1/mcp", "--header", "Authorization: Bearer ek_YOUR_KEY"]
    }
  }
}
```

## Example prompts

- "Validate this XRechnung and tell me what the supplier has to fix" (paste the XML).
- "Extract the line items and VAT breakdown of this Factur-X PDF as a table."
- "Is this Peppol invoice compliant with BIS Billing 3.0? List fatal errors only."
- "Render this KSeF invoice as a PDF in English."

## Pricing

Free tier 100 requests/month; Starter €24, Pro €49, Business €149 per month. Details: https://einvoicechecker.com/pricing/

## Files in this repository

- `server.json`: the registry manifest published to the official MCP registry.
- `server-card.json`: static server card also served at https://einvoicechecker.com/.well-known/mcp/server-card.json.

Terms: https://einvoicechecker.com/terms/ · Privacy: https://einvoicechecker.com/privacy/ · Support: support@einvoicechecker.com
