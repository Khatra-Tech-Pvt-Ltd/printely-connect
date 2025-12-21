# Printely Connect API Guide

![Version](https://img.shields.io/badge/version-1.0.3-blue.svg)
[![License](https://img.shields.io/badge/license-proprietary-red.svg)](../LICENSE)

## Overview
The REST API exposes secure endpoints for discovering printers, submitting print jobs, and monitoring system health. All operations are secured with API key authentication and governed by the [proprietary license](../LICENSE).

## Authentication

### Base URL
- **Local Development:** `http://localhost:5000`
- **Remote Deployment:** Use your secure remote tunnel URL

### Required Headers
All requests must include:
```
X-API-Key: <your-api-key>
Content-Type: application/json
```

### API Key Management
- Keys are issued and managed within the Printely Connect desktop/dashboard experience
- Missing or invalid keys result in `401 Unauthorized` responses
- Keep your API key secure and never commit it to version control
- Store keys in environment variables or secure configuration management systems

## Endpoints

### GET /api/printers – Get Available Printers

**Description:** Returns metadata for all configured printer devices.

**Request:**
```bash
curl -X GET http://localhost:5000/api/printers \
  -H "X-API-Key: your-api-key" \
  -H "Content-Type: application/json"
```

**Response (200 OK):**
```json
{
  "printers": [
    {
      "id": 1,
      "name": "Thermal Printer 1",
      "type": "USB",
      "status": "online",
      "model": "Epson TM-T20III"
    },
    {
      "id": 2,
      "name": "Network Printer",
      "type": "Network",
      "status": "offline",
      "model": "Star Micronics SM-T300"
    }
  ]
}
```

**Use Cases:**
- Health dashboards and monitoring
- Printer allocation logic
- Shift-opening device audits
- Availability checks before dispatching jobs

---

### POST /api/print – Simple Text Printing

**Description:** Send plain text directly to a printer without template formatting.

**Request:**
```bash
curl -X POST http://localhost:5000/api/print \
  -H "X-API-Key: your-api-key" \
  -H "Content-Type: application/json" \
  -d '{
    "printer_id": 1,
    "text": "Hello from Printely Connect!"
  }'
```

**Response (200 OK):**
```json
{
  "job_id": "job_abc123xyz",
  "status": "queued",
  "message": "Print job submitted successfully"
}
```

**Use Cases:**
- Lightweight receipts or test tickets
- Quick debugging and validation
- Simple notification printing

---

### POST /api/print – Template-Based Printing

**Description:** Submit templated print jobs with advanced formatting (alignment, bold, dates, currency, tables).

**Request:**
```bash
curl -X POST http://localhost:5000/api/print \
  -H "X-API-Key: your-api-key" \
  -H "Content-Type: application/json" \
  -d '{
    "printer_id": 1,
    "template": {
      "header": {
        "text": "Company Receipt",
        "align": "center",
        "bold": true
      },
      "line_items": {
        "type": "table",
        "columns": [{"name": "Item", "width": 20}, {"name": "Price", "width": 10}]
      }
    },
    "data": {
      "items": [{"name": "Service", "price": "$50.00"}],
      "total": "$50.00",
      "timestamp": "2025-12-21T10:30:00Z"
    }
  }'
```

**Response (200 OK):**
```json
{
  "job_id": "job_xyz789abc",
  "status": "queued",
  "message": "Templated print job submitted"
}
```

**Use Cases:**
- Branded receipts with company headers and footers
- Invoices with currency formatting and totals
- Order tickets with itemized details
- Labels with barcodes and custom layouts

---

### GET /api/status – Server Status

**Description:** Check overall system health, version, and printer availability.

**Request:**
```bash
curl -X GET http://localhost:5000/api/status \
  -H "X-API-Key: your-api-key" \
  -H "Content-Type: application/json"
```

**Response (200 OK):**
```json
{
  "status": "healthy",
  "version": "1.0.3",
  "uptime_seconds": 86400,
  "printers_connected": 2,
  "printers_online": 1,
  "printers_offline": 1,
  "timestamp": "2025-12-21T10:30:00Z"
}
```

**Use Cases:**
- Pre-job health checks
- Monitoring and alerting systems
- CI/CD smoke tests
- Dashboard status displays

## Template System

Templates allow you to define reusable printer layouts without embedding device-specific escape codes. This ensures consistency across all printers and simplifies branded receipt generation.

| Feature | Description | Example |
| --- | --- | --- |
| **Text Formatting** | Bold, italic, underline, alignment | `{ "bold": true, "align": "center" }` |
| **Date/Time Formatting** | Custom patterns and timezones | `{ "format": "%m/%d/%Y %H:%M", "timezone": "UTC" }` |
| **Number Formatting** | Currency, decimals, percentages | `{ "prefix": "$", "format": ".2f" }` |
| **Table Support** | Multi-column layouts with custom widths | `{ "type": "table", "columns": [{"name": "Item", "width": 20}] }` |
| **Conditional Logic** | Show/hide sections based on data | `{ "condition": "total > 0" }` |
| **Barcode/QR Codes** | Embedded codes for tracking | `{ "type": "barcode", "data": "SKU123" }` |

### Template Example

```json
{
  "template": {
    "width": 40,
    "header": {
      "text": "=== RECEIPT ===\",
      "align": "center",
      "bold": true
    },
    "sections": [
      {
        "label": "Date:",
        "value_format": "%Y-%m-%d %H:%M"
      },
      {
        "type": "table",
        "columns": [
          {"name": "Item", "width": 20},
          {"name": "Qty", "width": 5},
          {"name": "Price", "width": 10}
        ]
      },
      {
        "label": "Total:",
        "value_prefix": "$",
        "value_format": ".2f",
        "bold": true
      }
    ]
  }
}
```

## Rate Limiting & Best Practices

### Rate Limits
- API requests are limited to **1000 requests per hour** per API key
- Responses include `X-RateLimit-*` headers indicating your current quota
- Exceeding limits returns `429 Too Many Requests`

### Security Recommendations

✓ **DO:**
- Store API keys in environment variables, not in code
- Use HTTPS for all remote connections
- Rotate keys periodically
- Use specific keys per service/environment
- Monitor API usage in the dashboard

✗ **DON'T:**
- Share API keys across teams or services
- Log full request payloads with keys
- Commit keys to version control
- Use a single key for all environments

## Tabs & Filtering UI
The docs UI categorizes endpoints into Printers, Printing, Templates, and System tabs so developers can focus on relevant operations while reusing the same code examples.

## Error Handling

All API errors follow a standardized response format for consistent error handling across clients.

### Common Error Responses

#### 400 Bad Request
**Invalid payload or missing required fields**
```json
{
  "error": "Bad Request",
  "message": "Missing required field: printer_id",
  "code": "INVALID_PAYLOAD"
}
```

#### 401 Unauthorized
**Missing or invalid API key**
```json
{
  "error": "Invalid API key",
  "message": "Please check your X-API-Key header",
  "code": "AUTH_FAILED"
}
```

#### 404 Not Found
**Printer ID does not exist**
```json
{
  "error": "Printer Not Found",
  "message": "Printer with ID 999 is not configured",
  "code": "PRINTER_NOT_FOUND"
}
```

#### 500 Internal Server Error
**Printer offline/disconnected or service failure**
```json
{
  "error": "Printer Error",
  "message": "Printer is offline or disconnected",
  "code": "PRINTER_OFFLINE",
  "retry_after": 30
}
```

### Error Handling Best Practices

1. **Check HTTP Status Codes:** Always validate the response code before processing data
2. **Implement Retry Logic:** For 5xx errors, implement exponential backoff
3. **Log Error Codes:** Use the `code` field for monitoring and alerting
4. **Handle 401 Gracefully:** Refresh API keys or notify administrators
5. **Alert on 404:** Verify printer configuration in the dashboard
6. **Monitor Uptime:** Use `/api/status` to detect printer health issues

## License & Compliance

All API usage is governed by the **Printely Connect Proprietary Software License v1.0.3**.

### Key License Points

**✓ You Are Permitted To:**
- Use the API for your internal business operations
- Integrate with your own applications
- Deploy on devices you own or control

**✗ You Are NOT Permitted To:**
- Reverse engineer or decompile the service
- Redistribute or sublicense the API to third parties
- Conduct competitive benchmarking without written consent
- Remove or obscure copyright notices
- Use for unlawful purposes

### Audit & Monitoring
KhatraTech reserves the right to audit API usage to ensure compliance. Non-compliance may result in:
- Immediate API key revocation
- Legal action
- Service termination

**For full license details, see [LICENSE](../LICENSE)**

### Support & Contact
- **Email:** info@khatratech.com
- **Website:** www.khatratech.com
- **License Version:** 1.0.3
- **Effective Date:** August 1, 2025