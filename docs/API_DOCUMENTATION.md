# Printely Connect API Documentation

## Overview

The Printely Connect API provides a RESTful interface for managing thermal printers and sending print jobs remotely. All endpoints return JSON responses and use API key authentication.

**Base URL (Local):** `http://localhost:5000`  
**Base URL (Remote):** `https://your-subdomain.loca.lt`

---

## Table of Contents

- [Authentication](#authentication)
- [Response Format](#response-format)
- [Error Handling](#error-handling)
- [Rate Limiting](#rate-limiting)
- [Endpoints](#endpoints)
  - [Health Check](#health-check)
  - [Printers](#printers)
  - [Printing](#printing)
  - [Print Jobs](#print-jobs)
- [Template System](#template-system)
- [Examples](#examples)
- [SDKs and Libraries](#sdks-and-libraries)

---

## Authentication

All API endpoints (except `/health`) require authentication using an API key.

### Getting Your API Key

1. Open Printely Connect desktop app
2. Go to **Dashboard** tab
3. Copy the API key from the **API Configuration** card
4. Or regenerate a new key with the **🔄 Regenerate** button

### Using the API Key

Include the API key in the `X-API-Key` header:

```http
X-API-Key: your-api-key-here
```

### Example

```bash
curl -X GET http://localhost:5000/api/printers \
  -H "X-API-Key: 12345678-1234-1234-1234-123456789012"
```

### Security Notes

- ⚠️ Keep your API key secret
- ⚠️ Don't commit API keys to version control
- ⚠️ Regenerate keys if compromised
- ✅ Use HTTPS for remote access (Localtunnel provides this)

---

## Response Format

### Success Response

```json
{
  "status": "success",
  "data": { ... },
  "message": "Operation completed successfully"
}
```

### Error Response

```json
{
  "error": "Error message",
  "details": "Additional error details"
}
```

### HTTP Status Codes

| Code | Meaning | Description |
|------|---------|-------------|
| 200 | OK | Request successful |
| 201 | Created | Resource created |
| 400 | Bad Request | Invalid request parameters |
| 401 | Unauthorized | Invalid or missing API key |
| 404 | Not Found | Resource not found |
| 500 | Internal Server Error | Server error |

---

## Error Handling

### Common Errors

**Invalid API Key:**
```json
{
  "error": "Invalid or missing API key"
}
```

**Printer Not Found:**
```json
{
  "error": "Printer with ID 1 not found"
}
```

**Missing Parameters:**
```json
{
  "error": "Missing required parameters"
}
```

### Error Response Fields

- `error` (string): Human-readable error message
- `details` (string, optional): Additional error information
- `traceback` (string, optional): Stack trace (development only)

---

## Rate Limiting

Currently, there are no rate limits. However, best practices:

- Don't send more than 10 requests per second
- Implement exponential backoff for retries
- Cache printer lists when possible

---

## Endpoints

### Health Check

Check if the API server is running.

**Endpoint:** `GET /health`  
**Authentication:** None required

#### Request

```bash
curl http://localhost:5000/health
```

#### Response

```json
{
  "status": "ok",
  "timestamp": "2025-01-14T10:30:00.000Z"
}
```

---

### Printers

#### Get All Printers

Retrieve a list of all configured printers.

**Endpoint:** `GET /api/printers`  
**Authentication:** Required

##### Request

```bash
curl -X GET http://localhost:5000/api/printers \
  -H "X-API-Key: your-api-key"
```

##### Response

```json
[
  {
    "id": 1,
    "name": "Receipt Printer",
    "type": "THERMAL",
    "interface": "NETWORK",
    "ip_address": "192.168.1.100",
    "port": 9100,
    "status": "active"
  },
  {
    "id": 2,
    "name": "Kitchen Printer",
    "type": "THERMAL",
    "interface": "NETWORK",
    "ip_address": "192.168.1.101",
    "port": 9100,
    "status": "active"
  }
]
```

##### Response Fields

- `id` (integer): Unique printer identifier
- `name` (string): Printer name
- `type` (string): Printer type (THERMAL, RECEIPT, LABEL, OTHER)
- `interface` (string): Connection type (USB, NETWORK, SERIAL)
- `ip_address` (string): IP address (network printers only)
- `port` (integer): Port number (network printers only)
- `status` (string): Printer status (active, inactive)

---

#### Get Single Printer

Retrieve details of a specific printer.

**Endpoint:** `GET /api/printers/:id`  
**Authentication:** Required

##### Request

```bash
curl -X GET http://localhost:5000/api/printers/1 \
  -H "X-API-Key: your-api-key"
```

##### Response

```json
{
  "id": 1,
  "name": "Receipt Printer",
  "type": "THERMAL",
  "interface": "NETWORK",
  "ip_address": "192.168.1.100",
  "port": 9100,
  "status": "active",
  "created_at": "2025-01-14T10:00:00.000Z",
  "updated_at": "2025-01-14T10:00:00.000Z"
}
```

---

#### Test Printer

Send a test print to verify printer connectivity.

**Endpoint:** `POST /api/printers/:id/test`  
**Authentication:** Required

##### Request

```bash
curl -X POST http://localhost:5000/api/printers/1/test \
  -H "X-API-Key: your-api-key"
```

##### Response (Success)

```json
{
  "success": true,
  "message": "Test page sent successfully",
  "printer": "Receipt Printer"
}
```

##### Response (Failure)

```json
{
  "success": false,
  "message": "Connection refused",
  "error": "ECONNREFUSED"
}
```

---

### Printing

#### Print Text

Send plain text to a printer.

**Endpoint:** `POST /api/print`  
**Authentication:** Required

##### Request Body

```json
{
  "printer_id": 1,
  "text": "Hello, World!\nThank you for your business!"
}
```

##### Parameters

- `printer_id` (integer, optional): Printer ID (defaults to 1)
- `text` (string, required): Text to print

##### Request Example

```bash
curl -X POST http://localhost:5000/api/print \
  -H "X-API-Key: your-api-key" \
  -H "Content-Type: application/json" \
  -d '{
    "printer_id": 1,
    "text": "Hello, World!\nThank you!"
  }'
```

##### Response

```json
{
  "status": "success",
  "message": "Print job submitted",
  "printer": "Receipt Printer",
  "job_id": 123,
  "content": "Hello, World!\nThank you!"
}
```

---

#### Print Receipt (Template-Based)

Print formatted receipts using templates.

**Endpoint:** `POST /api/print`  
**Authentication:** Required

##### Request Body

```json
{
  "printer_id": 1,
  "template": {
    "store_name": {
      "align": "center",
      "bold": true
    },
    "divider1": {
      "char": "-",
      "length": 42
    },
    "date": {
      "prefix": "Date: ",
      "format": "MM/DD/YYYY HH:mm"
    },
    "items": {
      "header": {
        "columns": [
          {"text": "Item", "width": 20},
          {"text": "Qty", "width": 5, "align": "center"},
          {"text": "Price", "width": 8, "align": "right"},
          {"text": "Total", "width": 9, "align": "right"}
        ],
        "divider": true
      },
      "row": {
        "name": {"width": 20},
        "quantity": {"width": 5, "align": "center"},
        "price": {"width": 8, "align": "right", "format": ".2f"},
        "total": {"width": 9, "align": "right", "format": ".2f"}
      }
    },
    "divider2": {
      "char": "-",
      "length": 42
    },
    "subtotal": {
      "prefix": "Subtotal: $",
      "format": ".2f",
      "align": "right"
    },
    "tax": {
      "prefix": "Tax: $",
      "format": ".2f",
      "align": "right"
    },
    "divider3": {
      "char": "=",
      "length": 42
    },
    "total": {
      "prefix": "TOTAL: $",
      "format": ".2f",
      "align": "right",
      "bold": true
    },
    "divider4": {
      "char": "-",
      "length": 42
    },
    "thank_you": {
      "text": "Thank you for your business!",
      "align": "center"
    }
  },
  "data": {
    "store_name": "My Coffee Shop",
    "date": "2025-01-14T10:30:00",
    "items": [
      {
        "name": "Cappuccino",
        "quantity": 2,
        "price": 4.50,
        "total": 9.00
      },
      {
        "name": "Croissant",
        "quantity": 1,
        "price": 3.50,
        "total": 3.50
      }
    ],
    "subtotal": 12.50,
    "tax": 1.00,
    "total": 13.50
  }
}
```

##### Parameters

- `printer_id` (integer, optional): Printer ID (defaults to 1)
- `template` (object, required): Template definition
- `data` (object, required): Data to fill template

##### Request Example

```bash
curl -X POST http://localhost:5000/api/print \
  -H "X-API-Key: your-api-key" \
  -H "Content-Type: application/json" \
  -d @receipt.json
```

##### Response

```json
{
  "status": "success",
  "message": "Print job submitted",
  "printer": "Receipt Printer",
  "job_id": 124
}
```

---

### Print Jobs

#### Get Print Job History

Retrieve recent print jobs.

**Endpoint:** `GET /api/jobs`  
**Authentication:** Required

##### Query Parameters

- `limit` (integer, optional): Number of jobs to return (default: 100, max: 1000)

##### Request

```bash
curl -X GET "http://localhost:5000/api/jobs?limit=10" \
  -H "X-API-Key: your-api-key"
```

##### Response

```json
[
  {
    "id": 124,
    "printer_id": 1,
    "printer_name": "Receipt Printer",
    "content": "{\"template\":{...},\"data\":{...}}",
    "status": "completed",
    "created_at": "2025-01-14T10:30:00.000Z",
    "updated_at": "2025-01-14T10:30:01.000Z"
  },
  {
    "id": 123,
    "printer_id": 1,
    "printer_name": "Receipt Printer",
    "content": "Hello, World!",
    "status": "completed",
    "created_at": "2025-01-14T10:25:00.000Z",
    "updated_at": "2025-01-14T10:25:01.000Z"
  }
]
```

##### Response Fields

- `id` (integer): Job ID
- `printer_id` (integer): Printer ID
- `printer_name` (string): Printer name
- `content` (string): Print content
- `status` (string): Job status (pending, completed, failed)
- `created_at` (string): Creation timestamp
- `updated_at` (string): Last update timestamp

---

## Template System

### Overview

The template system allows you to create formatted receipts with:
- Text alignment (left, center, right)
- Bold text
- Dividers and separators
- Tables with headers and rows
- Number formatting
- Date/time formatting

### Template Structure

```json
{
  "field_name": {
    "align": "left|center|right",
    "bold": true|false,
    "prefix": "Text before value",
    "suffix": "Text after value",
    "format": "Number or date format",
    "text": "Static text"
  }
}
```

### Field Types

#### Simple Text Field

```json
{
  "store_name": {
    "align": "center",
    "bold": true
  }
}
```

Data:
```json
{
  "store_name": "My Store"
}
```

Output:
```
        My Store
```

#### Text with Prefix/Suffix

```json
{
  "total": {
    "prefix": "Total: $",
    "format": ".2f",
    "align": "right"
  }
}
```

Data:
```json
{
  "total": 25.99
}
```

Output:
```
              Total: $25.99
```

#### Static Text

```json
{
  "thank_you": {
    "text": "Thank you!",
    "align": "center"
  }
}
```

Output:
```
       Thank you!
```

#### Divider

```json
{
  "divider": {
    "char": "-",
    "length": 42
  }
}
```

Output:
```
------------------------------------------
```

#### Date/Time Field

```json
{
  "date": {
    "prefix": "Date: ",
    "format": "MM/DD/YYYY HH:mm"
  }
}
```

Data:
```json
{
  "date": "2025-01-14T10:30:00"
}
```

Output:
```
Date: 01/14/2025 10:30
```

#### Table

```json
{
  "items": {
    "header": {
      "columns": [
        {"text": "Item", "width": 20},
        {"text": "Qty", "width": 5, "align": "center"},
        {"text": "Price", "width": 8, "align": "right"}
      ],
      "divider": true
    },
    "row": {
      "name": {"width": 20},
      "quantity": {"width": 5, "align": "center"},
      "price": {"width": 8, "align": "right", "format": ".2f"}
    }
  }
}
```

Data:
```json
{
  "items": [
    {"name": "Coffee", "quantity": 2, "price": 4.50},
    {"name": "Muffin", "quantity": 1, "price": 3.00}
  ]
}
```

Output:
```
Item                 Qty    Price
----------------------------------
Coffee                 2     4.50
Muffin                 1     3.00
```

### Number Formatting

- `.2f` - Two decimal places (e.g., 25.99)
- `.0f` - No decimal places (e.g., 26)
- `.3f` - Three decimal places (e.g., 25.990)

### Date Formatting

- `MM/DD/YYYY` - 01/14/2025
- `DD/MM/YYYY` - 14/01/2025
- `YYYY-MM-DD` - 2025-01-14
- `HH:mm` - 10:30
- `HH:mm:ss` - 10:30:45
- `MM/DD/YYYY HH:mm` - 01/14/2025 10:30

### Alignment

- `left` - Left-aligned (default)
- `center` - Center-aligned
- `right` - Right-aligned

---

## Examples

### Example 1: Simple Text Print

```javascript
const axios = require('axios');

const API_URL = 'http://localhost:5000';
const API_KEY = 'your-api-key';

async function printText() {
  try {
    const response = await axios.post(`${API_URL}/api/print`, {
      printer_id: 1,
      text: 'Hello from Node.js!\nThank you!'
    }, {
      headers: {
        'X-API-Key': API_KEY,
        'Content-Type': 'application/json'
      }
    });
    
    console.log('Print successful:', response.data);
  } catch (error) {
    console.error('Print failed:', error.response?.data || error.message);
  }
}

printText();
```

### Example 2: Receipt Print

```javascript
async function printReceipt() {
  const receipt = {
    printer_id: 1,
    template: {
      store_name: { align: 'center', bold: true },
      divider1: { char: '-', length: 42 },
      date: { prefix: 'Date: ', format: 'MM/DD/YYYY HH:mm' },
      divider2: { char: '-', length: 42 },
      items: {
        header: {
          columns: [
            { text: 'Item', width: 20 },
            { text: 'Qty', width: 5, align: 'center' },
            { text: 'Price', width: 8, align: 'right' },
            { text: 'Total', width: 9, align: 'right' }
          ],
          divider: true
        },
        row: {
          name: { width: 20 },
          quantity: { width: 5, align: 'center' },
          price: { width: 8, align: 'right', format: '.2f' },
          total: { width: 9, align: 'right', format: '.2f' }
        }
      },
      divider3: { char: '-', length: 42 },
      subtotal: { prefix: 'Subtotal: $', format: '.2f', align: 'right' },
      tax: { prefix: 'Tax: $', format: '.2f', align: 'right' },
      divider4: { char: '=', length: 42 },
      total: { prefix: 'TOTAL: $', format: '.2f', align: 'right', bold: true },
      divider5: { char: '-', length: 42 },
      thank_you: { text: 'Thank you for your business!', align: 'center' }
    },
    data: {
      store_name: 'My Coffee Shop',
      date: new Date().toISOString(),
      items: [
        { name: 'Cappuccino', quantity: 2, price: 4.50, total: 9.00 },
        { name: 'Croissant', quantity: 1, price: 3.50, total: 3.50 }
      ],
      subtotal: 12.50,
      tax: 1.00,
      total: 13.50
    }
  };

  try {
    const response = await axios.post(`${API_URL}/api/print`, receipt, {
      headers: {
        'X-API-Key': API_KEY,
        'Content-Type': 'application/json'
      }
    });
    
    console.log('Receipt printed:', response.data);
  } catch (error) {
    console.error('Print failed:', error.response?.data || error.message);
  }
}

printReceipt();
```

### Example 3: Python

```python
import requests
import json
from datetime import datetime

API_URL = 'http://localhost:5000'
API_KEY = 'your-api-key'

def print_text(text):
    response = requests.post(
        f'{API_URL}/api/print',
        headers={
            'X-API-Key': API_KEY,
            'Content-Type': 'application/json'
        },
        json={
            'printer_id': 1,
            'text': text
        }
    )
    return response.json()

def print_receipt(items, subtotal, tax, total):
    receipt = {
        'printer_id': 1,
        'template': {
            'store_name': {'align': 'center', 'bold': True},
            'divider1': {'char': '-', 'length': 42},
            'date': {'prefix': 'Date: ', 'format': 'MM/DD/YYYY HH:mm'},
            'items': {
                'header': {
                    'columns': [
                        {'text': 'Item', 'width': 20},
                        {'text': 'Qty', 'width': 5, 'align': 'center'},
                        {'text': 'Price', 'width': 8, 'align': 'right'},
                        {'text': 'Total', 'width': 9, 'align': 'right'}
                    ],
                    'divider': True
                },
                'row': {
                    'name': {'width': 20},
                    'quantity': {'width': 5, 'align': 'center'},
                    'price': {'width': 8, 'align': 'right', 'format': '.2f'},
                    'total': {'width': 9, 'align': 'right', 'format': '.2f'}
                }
            },
            'divider2': {'char': '-', 'length': 42},
            'subtotal': {'prefix': 'Subtotal: $', 'format': '.2f', 'align': 'right'},
            'tax': {'prefix': 'Tax: $', 'format': '.2f', 'align': 'right'},
            'divider3': {'char': '=', 'length': 42},
            'total': {'prefix': 'TOTAL: $', 'format': '.2f', 'align': 'right', 'bold': True},
            'thank_you': {'text': 'Thank you!', 'align': 'center'}
        },
        'data': {
            'store_name': 'My Store',
            'date': datetime.now().isoformat(),
            'items': items,
            'subtotal': subtotal,
            'tax': tax,
            'total': total
        }
    }
    
    response = requests.post(
        f'{API_URL}/api/print',
        headers={
            'X-API-Key': API_KEY,
            'Content-Type': 'application/json'
        },
        json=receipt
    )
    return response.json()

# Usage
print_text('Hello from Python!')

items = [
    {'name': 'Coffee', 'quantity': 2, 'price': 4.50, 'total': 9.00},
    {'name': 'Muffin', 'quantity': 1, 'price': 3.00, 'total': 3.00}
]
print_receipt(items, 12.00, 0.96, 12.96)
```

### Example 4: PHP

```php
<?php

$apiUrl = 'http://localhost:5000';
$apiKey = 'your-api-key';

function printText($text) {
    global $apiUrl, $apiKey;
    
    $data = [
        'printer_id' => 1,
        'text' => $text
    ];
    
    $ch = curl_init("$apiUrl/api/print");
    curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
    curl_setopt($ch, CURLOPT_POST, true);
    curl_setopt($ch, CURLOPT_POSTFIELDS, json_encode($data));
    curl_setopt($ch, CURLOPT_HTTPHEADER, [
        'X-API-Key: ' . $apiKey,
        'Content-Type: application/json'
    ]);
    
    $response = curl_exec($ch);
    curl_close($ch);
    
    return json_decode($response, true);
}

// Usage
$result = printText("Hello from PHP!\nThank you!");
print_r($result);
?>
```

---

## SDKs and Libraries

### Official SDKs

Currently, there are no official SDKs. However, the API is simple REST/JSON and works with any HTTP client.

### Recommended Libraries

**Node.js:**
- axios
- node-fetch
- request (deprecated, use axios)

**Python:**
- requests
- httpx
- aiohttp (async)

**PHP:**
- Guzzle
- cURL (built-in)

**Java:**
- OkHttp
- Apache HttpClient

**C#:**
- HttpClient
- RestSharp

---

## Best Practices

### 1. Error Handling

Always handle errors gracefully:

```javascript
try {
  const response = await axios.post(url, data, config);
  console.log('Success:', response.data);
} catch (error) {
  if (error.response) {
    // Server responded with error
    console.error('Error:', error.response.data);
  } else if (error.request) {
    // No response received
    console.error('No response from server');
  } else {
    // Request setup error
    console.error('Error:', error.message);
  }
}
```

### 2. Retry Logic

Implement exponential backoff for retries:

```javascript
async function printWithRetry(data, maxRetries = 3) {
  for (let i = 0; i < maxRetries; i++) {
    try {
      return await axios.post(url, data, config);
    } catch (error) {
      if (i === maxRetries - 1) throw error;
      await new Promise(resolve => setTimeout(resolve, 1000 * Math.pow(2, i)));
    }
  }
}
```

### 3. Connection Pooling

Reuse HTTP connections:

```javascript
const axios = require('axios');

const client = axios.create({
  baseURL: 'http://localhost:5000',
  headers: {
    'X-API-Key': 'your-api-key'
  },
  timeout: 10000
});

// Use client for all requests
client.post('/api/print', data);
```

### 4. Validate Before Sending

Validate data before sending to API:

```javascript
function validateReceipt(receipt) {
  if (!receipt.printer_id) throw new Error('printer_id required');
  if (!receipt.template) throw new Error('template required');
  if (!receipt.data) throw new Error('data required');
  return true;
}
```

### 5. Log Requests

Log all API requests for debugging:

```javascript
axios.interceptors.request.use(request => {
  console.log('Request:', request.method, request.url);
  return request;
});

axios.interceptors.response.use(
  response => {
    console.log('Response:', response.status);
    return response;
  },
  error => {
    console.error('Error:', error.message);
    return Promise.reject(error);
  }
);
```

---

## Troubleshooting

### Common Issues

**401 Unauthorized**
- Check API key is correct
- Ensure `X-API-Key` header is set
- Regenerate API key if needed

**404 Not Found**
- Verify endpoint URL is correct
- Check printer ID exists
- Ensure server is running

**500 Internal Server Error**
- Check server logs
- Verify printer is reachable
- Check template syntax

**Connection Refused**
- Ensure server is running
- Check firewall settings
- Verify port is correct

**Timeout**
- Increase timeout setting
- Check network connectivity
- Verify printer is responding

---

## Support

Need help? Contact us:

- **📧 Email**: support-printelyconnect@khatratech.com
- **🌐 Website**: https://www.khatratech.com
- **📖 Docs**: https://github.com/khatratech/printely-connect
- **🐛 Issues**: https://github.com/khatratech/printely-connect/issues

---

**© 2025 KhatraTech. All rights reserved.**
