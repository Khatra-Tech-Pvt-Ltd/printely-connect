<div align="center">
    
# Printely Connect

![Printely Connect](assets/Printely_Connect.png)

[![Node.js Version](https://img.shields.io/badge/node-18%2B-brightgreen.svg)](https://nodejs.org/)
[![Electron](https://img.shields.io/badge/electron-35.7.5-blue.svg)](https://www.electronjs.org/)
[![Version](https://img.shields.io/badge/version-2.0.0-blue)](https://github.com/khatratech/printely-connect/releases)
[![Latest Release](https://img.shields.io/badge/download-latest-brightgreen.svg)](https://github.com/Khatra-Tech-Pvt-Ltd/printely-connect/releases/latest)
[![All Releases](https://img.shields.io/badge/releases-all-yellow.svg)](https://github.com/Khatra-Tech-Pvt-Ltd/printely-connect/releases)
[![License](https://img.shields.io/badge/license-proprietary-red.svg)](LICENSE)

</div>

**Printely Connect** is a powerful desktop application for managing thermal printers with REST API support and remote access capabilities. Built with Node.js and Electron, it provides a modern, cross-platform solution for businesses that require **secure, reliable, and programmable printing**.  
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
- Manage multiple network thermal printers
- Register and monitor printers from a single desktop agent
- Real-time availability and health checks via API
- ESC/POS compatible thermal printers support

### 🔐 Secure API-Driven Printing
- `X-API-Key` based authentication
- JSON-based request validation
- Safe for VPNs and secure tunnels
- Content Security Policy enabled

### 🧩 Smart Template Engine
- Reusable JSON print templates
- **NEW!** Visual Template Builder with drag-and-drop interface
- No ESC/POS logic inside business apps
- Consistent branding across devices
- Live preview and test printing

### 🌐 Remote Access
- Built-in Localtunnel integration for internet access
- Automatic subdomain persistence
- Access your printers from anywhere

### 💾 Zero Native Dependencies
- Pure JavaScript implementation
- No compilation needed
- Fast startup and deployment
- JSON-based persistent storage

### ⚙️ Production-Ready Error Handling
- Predictable HTTP responses (401, 404, 500)
- Designed for retries, alerts, and dashboards
- Comprehensive logging system

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
- Detect failures early using `/api/health`

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


## 💻 System Requirements

### Minimum Requirements
- **CPU**: 1 Core
- **RAM**: 512 MB
- **Disk Space**: 200 MB
- **OS**: Windows 10+, macOS 10.13+, or Linux (Ubuntu 18.04+)

### Supported Printers
- **Network Printers**: ESC/POS compatible thermal printers
- **Protocols**: TCP/IP (Port 9100 default)
- **Tested Brands**: Epson, Star Micronics, Zebra, Citizen

---

## ⚡ Quick Start

### Installation

**Download Pre-built App (Recommended)**

1. Download the latest [**Printely Connect**](https://github.com/Khatra-Tech-Pvt-Ltd/printely-connect/releases/latest) desktop application


### First Launch

1. The application will automatically:
   - ✅ Create necessary directories
   - ✅ Initialize the database
   - ✅ Generate an API key
   - ✅ Start the API server on port 5000
   - ✅ Open the desktop interface

2. **Get Your API Key**
   - Look at the **Dashboard** tab
   - Copy your API key from the **API Configuration** card

3. **Add a Printer**
   - Go to **Printers** tab
   - Click **➕ Add Printer**
   - Enter printer details (Name, IP Address, Port)
   - Click **Add Printer**

4. **Test Printing**
   - Select your printer in the **Print** tab
   - Enter some text
   - Click **🖨️ PRINT NOW**

5. **Enable Remote Access (Optional)**
   - Go to **Settings** tab
   - Check **Enable Localtunnel**
   - Your public URL appears in the Dashboard

### Verify Installation

```bash
# Check health
curl http://localhost:5000/health

# Get all available printers
curl -H "X-API-Key: YOUR_API_KEY" http://localhost:5000/api/printers

# Print test document
curl -X POST http://localhost:5000/api/print \
  -H "X-API-Key: YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"printer_id": 1, "text": "Hello World"}'
```

---

## 📘 Documentation

### Basic API Usage

**Base URLs:**
- **Local**: `http://localhost:5000`
- **Remote**: `https://your-subdomain.loca.lt` (with Localtunnel enabled)

**Authentication:**
All API endpoints (except `/health`) require an API key:
```bash
X-API-Key: your-api-key-here
```

**Quick Examples:**

```bash
# Health check (no auth required)
curl http://localhost:5000/health

# List registered printers
curl -H "X-API-Key: YOUR_API_KEY" \
  http://localhost:5000/api/printers

# Print text
curl -X POST http://localhost:5000/api/print \
  -H "X-API-Key: YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "printer_id": 1,
    "text": "Hello World!\nThank you!"
  }'

# Print receipt with template
curl -X POST http://localhost:5000/api/print \
  -H "X-API-Key: YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "printer_id": 1,
    "template": {
      "store_name": {"align": "center", "bold": true},
      "total": {"prefix": "Total: $", "format": ".2f"}
    },
    "data": {
      "store_name": "My Store",
      "total": 25.99
    }
  }'
```

### Complete Documentation
- **[API Reference](docs/API_DOCUMENTATION.md)** - Complete API endpoint documentation
- **[Template Guide](docs/TEMPLATE_GUIDE.md)** - Receipt template system guide

---

## 🔐 Security & Compliance

- API-key based authentication

- Designed for on-premise and controlled network environments

- Suitable for secure tunnels and private networks

- No cloud dependency required

---

## 📜 License

Printely Connect is proprietary software developed and owned by [Samrat Subedi](https://github.com/subedi-samrat) | [KhatraTech Pvt. Ltd](https://www.khatratech.com).

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
See [EULA](EULA.md) for End User License Agreement details.

---

## 🧑‍💼 Support & Contact

### Get Help
- **📧 Email**: support-printelyconnect@khatratech.com

- 🌐 Website: [Printely Connect](https://printely-connect.subedi-samrat.com.np)

© 2025 [KhatraTech Pvt. Ltd](https://www.khatratech.com). All rights reserved.

**Made with ❤️ by [Samrat Subedi](https://github.com/subedi-samrat) | [www.subedi-samrat.com.np](https://subedi-samrat.com.np)**
