# Printely Connect

![Printely Connect](assets/Printely_Connect.png)

[![Version](https://img.shields.io/badge/version-1.0.3-blue.svg)](https://github.com/Khatra-Tech-Pvt-Ltd/printely-connect/releases/tag/1.0.3)
[![Latest Release](https://img.shields.io/badge/download-latest-brightgreen.svg)](https://github.com/Khatra-Tech-Pvt-Ltd/printely-connect/releases/latest)
[![All Releases](https://img.shields.io/badge/releases-all-yellow.svg)](https://github.com/Khatra-Tech-Pvt-Ltd/printely-connect/releases)
[![License](https://img.shields.io/badge/license-proprietary-red.svg)](LICENSE)

**Printely Connect** is a desktop-first printer orchestration platform designed for businesses that require **secure, reliable, and programmable printing**.  
It exposes a **REST API** to control and manage thermal printers locally or remotely—without coupling your application logic to printer hardware.

---

## 🚀 What is Printely Connect?

Printely Connect acts as a **bridge between modern software systems and physical printers**.

- Developers integrate printing via HTTP APIs
- Operations teams maintain full control over printer hardware
- Businesses scale printing across locations without fragile drivers or scripts

---

## ✨ Core Features

### 🖨️ Multi-Printer Management
- Auto-discover USB, Network, and Serial printers
- Register and monitor printers from a single desktop agent
- Real-time availability and health checks via API

### 🔐 Secure API-Driven Printing
- `X-API-Key` based authentication
- JSON-based request validation
- Safe for VPNs and secure tunnels

### 🧩 Smart Template Engine
- Reusable JSON print templates
- No ESC/POS logic inside business apps
- Consistent branding across devices

### ⚙️ Production-Ready Error Handling
- Predictable HTTP responses (401, 404, 500)
- Designed for retries, alerts, and dashboards

---

## 🧠 Common Use Cases

### 🛒 Retail & Hospitality (POS)
Run Printely Connect locally at each outlet and trigger printing from:
- POS systems
- Cloud dashboards
- Central headquarters

Ideal for receipts, kitchen tickets, and order slips.

---

### 📦 Logistics & Warehousing
- Validate printer availability before batch processing
- Print shipping labels and manifests on demand
- Detect failures early using `/api/status`

---

### 🧩 Multi-Tenant SaaS Platforms
- Assign unique API keys per tenant
- Trigger invoice, ticket, or label printing remotely
- Printely Connect enforces tenant isolation at the agent level

---

### 🧪 Support & QA Testing
- Reproduce customer issues in controlled environments
- Validate templates and printer behavior
- Use documented APIs for deterministic testing

---


## ⚡ Quick Start

### Installation

1. Download the latest [**Printely Connect**](https://github.com/Khatra-Tech-Pvt-Ltd/printely-connect/releases/latest) desktop application
2. Install and launch the application
3. Configure your printers (USB / Network / Serial)
4. Copy your **API Key** from the dashboard
5. Verify the service is running
    ```bash
    curl -H "X-API-Key: YOUR_API_KEY" http://localhost:5000/api/status
    ```
6. Print a test document
    ```bash
    curl -X POST http://localhost:5000/api/print   -H "X-API-Key: YOUR_API_KEY"   -H "Content-Type: application/json"   -d '{"printer_id":"printer_001","content":"Hello World"}'
    ```

---

## 📘 Documentation

### Basic API Usage
```bash
# List registered printers
curl -H "X-API-Key: YOUR_API_KEY" \
http://localhost:5000/api/printers
```

```bash
# Submit a print job
curl -X POST http://localhost:5000/api/print \
  -H "X-API-Key: YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "printer_id": "printer_001",
    "content": "Hello World"
  }'
```

Complete endpoint documentation, request/response formats, and template syntax.
- [API Reference](docs/API.md)

---

## 🔐 Security & Compliance

- API-key based authentication

- Designed for on-premise and controlled network environments

- Suitable for secure tunnels and private networks

- No cloud dependency required

---

## 📜 License

Printely Connect is proprietary software developed and owned by [KhatraTech Pvt. Ltd](https://www.khatratech.com).

### Permitted Use

- Internal business operations
- Installation on owned or controlled systems
- Full access to intended application functionality

### Restrictions

- No redistribution or sublicensing
- No resale or commercial hosting
- No reverse engineering or derivative works
- No competitive benchmarking without written approval

### Legal

- Software provided “AS IS”
- Liability limited to fees paid
- Audit rights reserved
- Anonymized usage analytics may be collected
- Governed by the laws of Nepal

See [LICENSE](LICENSE) for full terms.

---

## 🧑‍💼 Support & Contact

For support, licensing, or feature requests:

- 📧 Email: info@khatratech.com

- 🌐 Website: https://www.khatratech.com

© 2025 [KhatraTech Pvt. Ltd](https://www.khatratech.com).
All rights reserved.

---
Developer : [Samrat Subedi](https://github.com/subedi-samrat)

Website : [printely-connect.subedi-samrat.com.np](https://printely-connect.subedi-samrat.com.np) 