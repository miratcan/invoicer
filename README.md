# Invoicer Static Generator

This project is a single-page invoice template (`index.html`) that reads data from URL parameters and renders a printable invoice. It is designed to be hosted at `https://mirat.dev/invoicer/`, but can be served from any static host.

## How It Works
- When the page loads it parses `window.location.search` and fills the invoice fields with any provided values.
- Line items are supplied as a URL-encoded JSON array and rendered into the invoice table. Subtotal, tax, and grand total are computed automatically.
- A **Print Invoice** button opens the browser print dialog; the layout includes print-specific styling.
- A built-in **Parameter Builder** (open via the "Open Parameter Builder" button) lets you enter all fields and line items interactively, then either copy the generated URL or apply it via "Build & Reload" without changing the underlying URL-driven behavior.

## Parameters
All parameters are optional; sensible defaults are used when a parameter is missing.

| Parameter | Description | Example |
|-----------|-------------|---------|
| `title` | Header text for the invoice | `ACME Studios Invoice` |
| `invoiceNumber` | Invoice identifier | `INV-2024-104` |
| `invoiceDate` | Issue date (any readable format) | `2024-05-01` |
| `dueDate` | Payment due date | `2024-05-15` |
| `fromTitle` | Label for the sender block | `From:` |
| `companyName` | Sender company name | `ACME Studios` |
| `companyAddress` | Sender address | `123 Market St., San Francisco` |
| `companyPhone` | Sender phone number | `+1 555 011 0234` |
| `companyEmail` | Sender email | `accounts@acmestudios.com` |
| `clientName` | Recipient name | `Mirat LLC` |
| `clientAddress` | Recipient address | `900 Innovation Dr., Istanbul` |
| `clientPhone` | Recipient phone | `+90 555 123 4567` |
| `clientEmail` | Recipient email | `invoice@miratllc.com` |
| `currencySymbol` | Symbol prefixed to monetary values | `$` |
| `taxRate` | Tax percentage applied to subtotal | `18` |
| `footerNote` | Message displayed at the bottom of the invoice | `Please send payment via wire transfer within 7 days.` |
| `logoUrl` | Absolute URL to the company logo image | `https://dummyimage.com/...` |
| `items` | URL-encoded JSON array of objects with `desc`, `qty`, and `rate` | `%5B%7B"desc"...%7D%5D` |

### Items Parameter Format
`items` must be URL-encoded JSON. The decoded structure looks like:

```json
[
  { "desc": "Web design services", "qty": 10, "rate": 80 },
  { "desc": "Hosting (3 months)", "qty": 1, "rate": 45 },
  { "desc": "Maintenance retainer", "qty": 2, "rate": 150 }
]
```

Subtotal uses `qty * rate` for each item. `taxRate` is interpreted as a percentage (e.g., `18` = 18%). `currencySymbol` defaults to `$` if omitted.

## Example URL
```
https://mirat.dev/invoicer/index.html?title=ACME+Studios+Invoice&invoiceNumber=INV-2024-104&invoiceDate=2024-05-01&dueDate=2024-05-15&fromTitle=From%3A&companyName=ACME+Studios&companyAddress=123+Market+St.%2C+Suite+500%2C+San+Francisco%2C+CA&companyPhone=%2B1+555+011+0234&companyEmail=accounts%40acmestudios.com&clientName=Mirat+LLC&clientAddress=900+Innovation+Dr.%2C+Istanbul%2C+Turkey&clientPhone=%2B90+555+123+4567&clientEmail=invoice%40miratllc.com&currencySymbol=%24&taxRate=18&footerNote=Please+send+payment+via+wire+transfer+within+7+days.&logoUrl=https%3A%2F%2Fdummyimage.com%2F150x150%2F007bff%2Fffffff.png%26text%3DACME&items=%5B%7B%22desc%22%3A%20%22Web%20design%20services%22%2C%20%22qty%22%3A%2010%2C%20%22rate%22%3A%2080%7D%2C%20%7B%22desc%22%3A%20%22Hosting%20%283%20months%29%22%2C%20%22qty%22%3A%201%2C%20%22rate%22%3A%2045%7D%2C%20%7B%22desc%22%3A%20%22Maintenance%20retainer%22%2C%20%22qty%22%3A%202%2C%20%22rate%22%3A%20150%7D%5D
```

## Prompting Guidance for AI Tools
Use the following prompt (or adapt it) to let a custom GPT or similar agent generate invoice URLs:

> You are an invoicing assistant. Produce a URL that loads `https://mirat.dev/invoicer/index.html` with query parameters populated for the invoice fields. Always include:
> - `title`, `invoiceNumber`, `invoiceDate`, `dueDate`
> - Sender details (`fromTitle`, `companyName`, `companyAddress`, `companyPhone`, `companyEmail`)
> - Client details (`clientName`, `clientAddress`, `clientPhone`, `clientEmail`)
> - Financial data (`currencySymbol`, `taxRate`, `items` as URL-encoded JSON of `{ "desc", "qty", "rate" }` objects) and optional `footerNote` or `logoUrl` if provided.
> Encode the `items` parameter with URL encoding (e.g., using `encodeURIComponent`). Return the complete URL ready to be opened in a browser.

This prompt ensures the AI tool supplies all relevant fields and encodes complex data correctly.

## Local Testing
Open the file directly in a browser or serve from a simple HTTP server:

```bash
python3 -m http.server 8000
```

Then visit `http://localhost:8000/index.html?...` with the desired parameters.

