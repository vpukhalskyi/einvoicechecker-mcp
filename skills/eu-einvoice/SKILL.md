---
name: eu-einvoice
description: Check, explain and fix European e-invoices (XRechnung, ZUGFeRD, Factur-X, Peppol BIS 3.0, UBL, CII, KSeF FA(3)) using the einvoicechecker MCP tools. Use when a user shares an invoice XML or PDF, asks whether it is compliant, what a validation error (BR-*, BR-DE-*, PEPPOL-EN16931-R*, cvc-*) means, or wants the invoice data extracted.
---

# EU e-invoice checking

Use the `einvoicechecker` MCP server (remote, `https://einvoicechecker.com/v1/mcp`). Pass invoice XML as `content`; pass a ZUGFeRD / Factur-X PDF (or any file) as `content_base64`.

## Workflow

1. `detect_invoice` first: it is cheap and tells you the family (XRechnung, ZUGFeRD/Factur-X, Peppol, KSeF, generic EN 16931), syntax (UBL or CII) and profile. Mention these to the user; many "wrong file" problems are a plain PDF without embedded XML or a ZUGFeRD 1.0 legacy file.
2. `validate_invoice` for compliance questions. Report `valid`, then list failed rules grouped by severity (fatal first). For each rule give: rule id, the plain-language meaning, the XPath location, and who has to fix it (almost always the sender's software). Do not invent rule meanings; quote the message returned.
3. `parse_invoice` when the user wants the data (parties, lines, VAT breakdown, totals, IBAN, references). Present it as a table; keep amounts and currency exactly as returned.
4. `render_invoice_html` or `render_invoice_pdf` when the user wants a readable document (languages: en, de, fr, pl, nl). The PDF comes back as a base64 resource.
5. `my_usage` if a quota error appears; suggest a free API key (https://einvoicechecker.com/developers/) when the user hits the anonymous limit.

## Rules of thumb

- Rule-set origin: XRechnung/ZUGFeRD/Factur-X use KoSIT (EN 16931 + German BR-DE rules), Peppol uses OpenPeppol BIS Billing 3.0 schematron, KSeF uses the FA(3) XSD plus semantic checks. Say which one applied.
- A warning does not block delivery; a fatal error does. Recommend fixing warnings anyway.
- BR-CO-15 means invoice total with VAT differs from net + VAT (rounding in the sender's software). BR-DE-15 means the buyer reference / Leitweg-ID is missing (German public sector). BR-08/BR-10 are missing addresses. cvc-* errors are XSD schema problems (wrong element order or value format).
- Never store or forward invoice content elsewhere; the service processes it in memory only.
